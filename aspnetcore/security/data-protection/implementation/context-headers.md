---
title: Kontextová záhlaví v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace hlavičky kontextu ochranu dat ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814018"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="bb6e7-103">Kontextová záhlaví v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb6e7-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="bb6e7-104">Na pozadí a teorii</span><span class="sxs-lookup"><span data-stu-id="bb6e7-104">Background and theory</span></span>

<span data-ttu-id="bb6e7-105">V systému ochrany dat "klíče" znamená, že objekt, který může poskytnout ověřeného šifrování služby.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="bb6e7-106">Každý klíč je identifikován jedinečný identifikátor (GUID), a provede s ní vylepšením informace a entropic materiálu.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="bb6e7-107">Předpokládá se, že každý klíč mají jedinečné entropie, ale systém nemůže vynutit, který a musíme také účet pro vývojáře, kteří můžou aktualizační kanál, který klíč ručně změnit úpravou vylepšením informace existujícího klíče v aktualizační kanál, který klíč.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="bb6e7-108">K dosažení zadané tyto případy naše požadavky na zabezpečení systému ochrany dat zahrnuje koncept [kryptografická Flexibilita](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), což umožňuje bezpečně pomocí jedné hodnoty entropic napříč více kryptografických algoritmů.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="bb6e7-109">Většina systémů, které podporují kryptografická flexibilita to tak, že včetně některých identifikační informace o algoritmu uvnitř datové části.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="bb6e7-110">Tento algoritmus OID je obecně vhodným kandidátem pro to.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="bb6e7-111">Jeden problém, který jsme narazili na je však, že existuje několik způsobů, jak zadat stejný algoritmus: "AES" (CNG) a spravovaný Aes, AesManaged, AesCryptoServiceProvider, AesCng a RijndaelManaged (zadané konkrétní parametry) třídy jsou všechny skutečně stejnou věc, a jsme bude nutné udržovat mapování všechny tyto na správný identifikátor objektu.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="bb6e7-112">Vývojář chtěli poskytnout vlastní algoritmus (nebo dokonce pro jiný provádění AES!), mají by nám můžete sdělit své OID.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="bb6e7-113">Tento krok navíc registrace díky konfiguraci systému zejména obtížná.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="bb6e7-114">Vracení, jsme se rozhodli, že jsme se blíží problém z nesprávného směr.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="bb6e7-115">Identifikátor OID Určuje, co je algoritmus, ale záleží nám na nejsou ve skutečnosti to.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="bb6e7-116">Pokud bychom někdy potřebovali bezpečněji používat hodnotu single entropic ve dvou různých algoritmů, není nutné nám vědět, co ve skutečnosti jsou algoritmy.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="bb6e7-117">Co jsme skutečně záleží spočívá v jejich chování.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="bb6e7-118">Libovolný vrazíme bloku symetrický šifrovací algoritmus je také silné pseudonáhodných permutací (PRP): Opravte vstupy (klíče, řetězení ve formátu prostého textu režimu, IV) a výstup šifrovaného textu s zahlcení pravděpodobnost budou lišit od jiných symetrický blokových šifrách algoritmus na stejném základě vstupů.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="bb6e7-119">Podobně všechny funkce vrazíme algoritmu hash s klíčem je také silné pseudonáhodných – funkce (PRF) a daný pevně danou sadu vstupních jeho výstup drtivá většina budou lišit od jakékoli jiné funkce algoritmu hash s klíčem.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="bb6e7-120">Tento koncept silné PRPs a PRFs používáme k vytvoření hlavičku kontextu.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="bb6e7-121">Tuto hlavičku kontextu v podstatě funguje jako stabilní kryptografický otisk přes algoritmy používané pro jakoukoli danou operaci, a poskytuje kryptografická flexibilita systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="bb6e7-122">Tato hlavička se reprodukovatelné a se později používá jako součást [podklíče procesu odvození](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="bb6e7-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="bb6e7-123">Existují dva různé způsoby, hlavičku kontextu v závislosti na režimy fungování algoritmů základní sestavení.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="bb6e7-124">Režimu CBC šifrování + ověřování HMAC</span><span class="sxs-lookup"><span data-stu-id="bb6e7-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="bb6e7-125">Hlavičku kontextu se skládá z následujících součástí:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="bb6e7-126">[16 bitů] Hodnota 00 00, což je značka znamená "CBC šifrování + ověřování HMAC".</span><span class="sxs-lookup"><span data-stu-id="bb6e7-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="bb6e7-127">[32 bitů] Délka klíče (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="bb6e7-128">[32 bitů] Velikost bloku (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="bb6e7-129">[32 bitů] Délka klíče (v bajtech, formát big-endian) algoritmus HMAC.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="bb6e7-130">(Aktuálně velikost klíče vždy odpovídá velikosti digest.)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="bb6e7-131">[32 bitů] Velikost digest (v bajtech, formát big-endian) algoritmus HMAC.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="bb6e7-132">EncCBC (K_E, IV, ""), což je výstup šifrovací algoritmus symetrického bloku vzhledem zadání prázdného řetězce a kde je je všechny nulové vektor IV.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="bb6e7-133">Konstrukce K_E je popsána níže.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="bb6e7-134">MAC (K_H, ""), což je výstup algoritmus HMAC vzhledem zadání prázdného řetězce.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="bb6e7-135">Konstrukce K_H je popsána níže.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="bb6e7-136">V ideálním případě jsme mohli předat všechny nulové vektory K_E a K_H.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="bb6e7-137">Však chceme, aby se zabránilo situaci, kdy algoritmus zkontroluje existenci slabé klíče před provedením jakékoli operace (zejména DES a 3DES), který vylučuje pomocí jednoduché nebo repeatable vzoru, jako je vektor všemi nulovými.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="bb6e7-138">Místo toho v režimu čítač používáme KDF SP800 108 NIST (naleznete v tématu [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), s. 5.1) s nulovou délkou klíče, popisek a kontextu a HMACSHA512 jako základní PRF.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="bb6e7-139">Odvodíme | K_E | + | K_H | bajty výstupu, potom rozloží výsledek na K_E a K_H sami.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="bb6e7-140">Matematický je reprezentováno následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="bb6e7-141">(K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = "")</span><span class="sxs-lookup"><span data-stu-id="bb6e7-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="bb6e7-142">Příklad: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="bb6e7-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="bb6e7-143">Jako příklad vezměte si situaci, kde šifrovací algoritmus symetrického bloku je AES-192-CBC a algoritmus pro ověření je HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="bb6e7-144">Systém vygeneruje hlavičku kontextu. použijte následující postup.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="bb6e7-145">Nejprve nechat (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = ""), kde | K_E | = 192 bitů a | K_H | = 256 bitů na zadané algoritmy.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="bb6e7-146">To vede k K_E = 5BB6... 21DD a K_H = A04A... 00A9 v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="bb6e7-147">V dalším kroku compute Enc_CBC (K_E, IV, "") AES-192-CBC zadaný IV = 0 \* a K_E jak je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="bb6e7-148">result := F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="bb6e7-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="bb6e7-149">V dalším kroku compute MAC (K_H, "") pro HMACSHA256 zadaný K_H jak je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="bb6e7-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="bb6e7-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="bb6e7-151">Tímto se vytvoří hlavičku úplný kontext níže:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="bb6e7-152">Tuto hlavičku kontextu je kryptografický algoritmus dvojice ověřené šifrování (AES-192-CBC šifrování + ověřování HMACSHA256).</span><span class="sxs-lookup"><span data-stu-id="bb6e7-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="bb6e7-153">Součásti, jak je popsáno [nad](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) jsou:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="bb6e7-154">značky (00 00)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-154">the marker (00 00)</span></span>

* <span data-ttu-id="bb6e7-155">Délka klíče šifrování bloku (00 00-00 18)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="bb6e7-156">velikost bloku šifrování bloku (00 00-00 10)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="bb6e7-157">Délka klíče HMAC (00 00-00 20)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="bb6e7-158">ověřování algoritmem digest velikost HMAC (00 00-00 20)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="bb6e7-159">Bloková šifra výstup zásad replikace HESEL (F4 74 – 6 DB f) a</span><span class="sxs-lookup"><span data-stu-id="bb6e7-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="bb6e7-160">výstup HMAC PRF (D4 79 - end).</span><span class="sxs-lookup"><span data-stu-id="bb6e7-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="bb6e7-161">Režimu CBC šifrování + HMAC tvoříme tak stejným způsobem bez ohledu na to, jestli jsou poskytnuty implementace algoritmy, Windows CNG nebo spravované typy SymmetricAlgorithm a KeyedHashAlgorithm ověřovací hlavičku kontextu.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="bb6e7-162">Díky tomu aplikace spuštěné v různých operačních systémech pro spolehlivé vytváření stejné hlavičku kontextu, i když implementace algoritmy se liší mezi verzemi operačních systémů.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="bb6e7-163">(V praxi však KeyedHashAlgorithm nemusí být správné HMAC.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="bb6e7-164">Může být libovolného typu algoritmu hash s klíčem.)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="bb6e7-165">Příklad: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="bb6e7-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="bb6e7-166">Nejprve nechat (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = ""), kde | K_E | = 192 bitů a | K_H | = 160 bitů na zadané algoritmy.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="bb6e7-167">To vede k K_E = A219... E2BB a K_H = DC4A... B464 v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="bb6e7-168">V dalším kroku compute Enc_CBC (K_E, IV, "") pro algoritmus 3DES-192-CBC zadaný IV = 0 \* a K_E jak je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="bb6e7-169">výsledek: = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="bb6e7-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="bb6e7-170">V dalším kroku compute MAC (K_H, "") pro HMACSHA1 zadaný K_H jak je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="bb6e7-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="bb6e7-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="bb6e7-172">Tímto se vytvoří hlavičku úplný kontext, který je kryptografický otisk ověřeného šifrování pár algoritmus (3DES-192-CBC šifrování + ověřování HMACSHA1), je uvedeno níže:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="bb6e7-173">Součásti rozdělit následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-173">The components break down as follows:</span></span>

* <span data-ttu-id="bb6e7-174">značky (00 00)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-174">the marker (00 00)</span></span>

* <span data-ttu-id="bb6e7-175">Délka klíče šifrování bloku (00 00-00 18)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="bb6e7-176">velikost bloku šifrování bloku (00 00-00 08)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="bb6e7-177">Délka klíče HMAC (00 00-00 14)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="bb6e7-178">ověřování algoritmem digest velikost HMAC (00 00-00 14)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="bb6e7-179">Bloková šifra výstup zásad replikace HESEL (AB B1 - E1 0E) a</span><span class="sxs-lookup"><span data-stu-id="bb6e7-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="bb6e7-180">výstup HMAC PRF (76 EB - end).</span><span class="sxs-lookup"><span data-stu-id="bb6e7-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="bb6e7-181">Režim Galois/čítač šifrování + ověřování</span><span class="sxs-lookup"><span data-stu-id="bb6e7-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="bb6e7-182">Hlavičku kontextu se skládá z následujících součástí:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="bb6e7-183">[16 bitů] Hodnota 00 01, což je značka znamená "GCM šifrování + ověřování".</span><span class="sxs-lookup"><span data-stu-id="bb6e7-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="bb6e7-184">[32 bitů] Délka klíče (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="bb6e7-185">[32 bitů] Její velikost (v bajtech, formát big-endian) používané během operace ověřeného šifrování.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="bb6e7-186">(Pro náš systém tento problém je vyřešený v nonce velikosti = 96 bits.)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="bb6e7-187">[32 bitů] Velikost bloku (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="bb6e7-188">(Pro GCM, tento problém je vyřešený v velikosti bloku = 128 bitů.)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="bb6e7-189">[32 bitů] Ověřování tag velikost (v bajtech, formát big-endian) vytvořeného funkcí ověřené šifrování.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="bb6e7-190">(Pro náš systém tento problém je vyřešený v velikost značky = 128 bitů.)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="bb6e7-191">[128 bitů] Značka Enc_GCM (K_E nonce, ""), což je výstup šifrovací algoritmus symetrického bloku vzhledem zadání prázdného řetězce a kde hodnota nonce je 96bitový vektor všechny nulové.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="bb6e7-192">K_E je odvozen použitím shodný mechanismus, stejně jako v CBC šifrování + HMAC ověřovacím scénáři.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="bb6e7-193">Ale protože zde není žádná K_H, v podstatě máme | K_H | = 0, a algoritmus Hroutí k níže uvedeného formuláře.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="bb6e7-194">K_E = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = "")</span><span class="sxs-lookup"><span data-stu-id="bb6e7-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="bb6e7-195">Příklad: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="bb6e7-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="bb6e7-196">Nejprve nechat K_E = SP800_108_CTR (prf = HMACSHA512, key = "", popisek = "", kontext = ""), kde | K_E | = 256 bitů.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="bb6e7-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="bb6e7-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="bb6e7-198">V dalším kroku compute ověřování značka Enc_GCM (K_E nonce, "") pro AES-256-GCM zadané hodnoty nonce = 096 a K_E jak je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="bb6e7-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="bb6e7-199">výsledek: = E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="bb6e7-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="bb6e7-200">Tímto se vytvoří hlavičku úplný kontext níže:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="bb6e7-201">Součásti rozdělit následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="bb6e7-201">The components break down as follows:</span></span>

* <span data-ttu-id="bb6e7-202">značky (00 01)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-202">the marker (00 01)</span></span>

* <span data-ttu-id="bb6e7-203">Délka klíče šifrování bloku (00 00-00 20)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="bb6e7-204">její velikost (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="bb6e7-205">velikost bloku šifrování bloku (00 00-00 10)</span><span class="sxs-lookup"><span data-stu-id="bb6e7-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="bb6e7-206">velikost značky ověřování (00 00-00 10) a</span><span class="sxs-lookup"><span data-stu-id="bb6e7-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="bb6e7-207">ověřovací značka ve spuštění blokových šifrách (řadič domény E7 - end).</span><span class="sxs-lookup"><span data-stu-id="bb6e7-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
