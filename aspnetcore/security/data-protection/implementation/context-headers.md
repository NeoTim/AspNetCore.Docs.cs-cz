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
# <a name="context-headers-in-aspnet-core"></a>Kontextová záhlaví v ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Na pozadí a teorii

V systému ochrany dat "klíče" znamená, že objekt, který může poskytnout ověřeného šifrování služby. Každý klíč je identifikován jedinečný identifikátor (GUID), a provede s ní vylepšením informace a entropic materiálu. Předpokládá se, že každý klíč mají jedinečné entropie, ale systém nemůže vynutit, který a musíme také účet pro vývojáře, kteří můžou aktualizační kanál, který klíč ručně změnit úpravou vylepšením informace existujícího klíče v aktualizační kanál, který klíč. K dosažení zadané tyto případy naše požadavky na zabezpečení systému ochrany dat zahrnuje koncept [kryptografická Flexibilita](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), což umožňuje bezpečně pomocí jedné hodnoty entropic napříč více kryptografických algoritmů.

Většina systémů, které podporují kryptografická flexibilita to tak, že včetně některých identifikační informace o algoritmu uvnitř datové části. Tento algoritmus OID je obecně vhodným kandidátem pro to. Jeden problém, který jsme narazili na je však, že existuje několik způsobů, jak zadat stejný algoritmus: "AES" (CNG) a spravovaný Aes, AesManaged, AesCryptoServiceProvider, AesCng a RijndaelManaged (zadané konkrétní parametry) třídy jsou všechny skutečně stejnou věc, a jsme bude nutné udržovat mapování všechny tyto na správný identifikátor objektu. Vývojář chtěli poskytnout vlastní algoritmus (nebo dokonce pro jiný provádění AES!), mají by nám můžete sdělit své OID. Tento krok navíc registrace díky konfiguraci systému zejména obtížná.

Vracení, jsme se rozhodli, že jsme se blíží problém z nesprávného směr. Identifikátor OID Určuje, co je algoritmus, ale záleží nám na nejsou ve skutečnosti to. Pokud bychom někdy potřebovali bezpečněji používat hodnotu single entropic ve dvou různých algoritmů, není nutné nám vědět, co ve skutečnosti jsou algoritmy. Co jsme skutečně záleží spočívá v jejich chování. Libovolný vrazíme bloku symetrický šifrovací algoritmus je také silné pseudonáhodných permutací (PRP): Opravte vstupy (klíče, řetězení ve formátu prostého textu režimu, IV) a výstup šifrovaného textu s zahlcení pravděpodobnost budou lišit od jiných symetrický blokových šifrách algoritmus na stejném základě vstupů. Podobně všechny funkce vrazíme algoritmu hash s klíčem je také silné pseudonáhodných – funkce (PRF) a daný pevně danou sadu vstupních jeho výstup drtivá většina budou lišit od jakékoli jiné funkce algoritmu hash s klíčem.

Tento koncept silné PRPs a PRFs používáme k vytvoření hlavičku kontextu. Tuto hlavičku kontextu v podstatě funguje jako stabilní kryptografický otisk přes algoritmy používané pro jakoukoli danou operaci, a poskytuje kryptografická flexibilita systému ochrany dat. Tato hlavička se reprodukovatelné a se později používá jako součást [podklíče procesu odvození](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Existují dva různé způsoby, hlavičku kontextu v závislosti na režimy fungování algoritmů základní sestavení.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Režimu CBC šifrování + ověřování HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Hlavičku kontextu se skládá z následujících součástí:

* [16 bitů] Hodnota 00 00, což je značka znamená "CBC šifrování + ověřování HMAC".

* [32 bitů] Délka klíče (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.

* [32 bitů] Velikost bloku (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.

* [32 bitů] Délka klíče (v bajtech, formát big-endian) algoritmus HMAC. (Aktuálně velikost klíče vždy odpovídá velikosti digest.)

* [32 bitů] Velikost digest (v bajtech, formát big-endian) algoritmus HMAC.

* EncCBC (K_E, IV, ""), což je výstup šifrovací algoritmus symetrického bloku vzhledem zadání prázdného řetězce a kde je je všechny nulové vektor IV. Konstrukce K_E je popsána níže.

* MAC (K_H, ""), což je výstup algoritmus HMAC vzhledem zadání prázdného řetězce. Konstrukce K_H je popsána níže.

V ideálním případě jsme mohli předat všechny nulové vektory K_E a K_H. Však chceme, aby se zabránilo situaci, kdy algoritmus zkontroluje existenci slabé klíče před provedením jakékoli operace (zejména DES a 3DES), který vylučuje pomocí jednoduché nebo repeatable vzoru, jako je vektor všemi nulovými.

Místo toho v režimu čítač používáme KDF SP800 108 NIST (naleznete v tématu [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), s. 5.1) s nulovou délkou klíče, popisek a kontextu a HMACSHA512 jako základní PRF. Odvodíme | K_E | + | K_H | bajty výstupu, potom rozloží výsledek na K_E a K_H sami. Matematický je reprezentováno následujícím způsobem.

(K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Příklad: AES-192-CBC + HMACSHA256

Jako příklad vezměte si situaci, kde šifrovací algoritmus symetrického bloku je AES-192-CBC a algoritmus pro ověření je HMACSHA256. Systém vygeneruje hlavičku kontextu. použijte následující postup.

Nejprve nechat (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = ""), kde | K_E | = 192 bitů a | K_H | = 256 bitů na zadané algoritmy. To vede k K_E = 5BB6... 21DD a K_H = A04A... 00A9 v následujícím příkladu:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

V dalším kroku compute Enc_CBC (K_E, IV, "") AES-192-CBC zadaný IV = 0 * a K_E jak je uvedeno výše.

result := F474B1872B3B53E4721DE19C0841DB6F

V dalším kroku compute MAC (K_H, "") pro HMACSHA256 zadaný K_H jak je uvedeno výše.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Tímto se vytvoří hlavičku úplný kontext níže:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Tuto hlavičku kontextu je kryptografický algoritmus dvojice ověřené šifrování (AES-192-CBC šifrování + ověřování HMACSHA256). Součásti, jak je popsáno [nad](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) jsou:

* značky (00 00)

* Délka klíče šifrování bloku (00 00-00 18)

* velikost bloku šifrování bloku (00 00-00 10)

* Délka klíče HMAC (00 00-00 20)

* ověřování algoritmem digest velikost HMAC (00 00-00 20)

* Bloková šifra výstup zásad replikace HESEL (F4 74 – 6 DB f) a

* výstup HMAC PRF (D4 79 - end).

> [!NOTE]
> Režimu CBC šifrování + HMAC tvoříme tak stejným způsobem bez ohledu na to, jestli jsou poskytnuty implementace algoritmy, Windows CNG nebo spravované typy SymmetricAlgorithm a KeyedHashAlgorithm ověřovací hlavičku kontextu. Díky tomu aplikace spuštěné v různých operačních systémech pro spolehlivé vytváření stejné hlavičku kontextu, i když implementace algoritmy se liší mezi verzemi operačních systémů. (V praxi však KeyedHashAlgorithm nemusí být správné HMAC. Může být libovolného typu algoritmu hash s klíčem.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Příklad: 3DES-192-CBC + HMACSHA1

Nejprve nechat (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = ""), kde | K_E | = 192 bitů a | K_H | = 160 bitů na zadané algoritmy. To vede k K_E = A219... E2BB a K_H = DC4A... B464 v následujícím příkladu:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

V dalším kroku compute Enc_CBC (K_E, IV, "") pro algoritmus 3DES-192-CBC zadaný IV = 0 * a K_E jak je uvedeno výše.

výsledek: = ABB100F81E53E10E

V dalším kroku compute MAC (K_H, "") pro HMACSHA1 zadaný K_H jak je uvedeno výše.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Tímto se vytvoří hlavičku úplný kontext, který je kryptografický otisk ověřeného šifrování pár algoritmus (3DES-192-CBC šifrování + ověřování HMACSHA1), je uvedeno níže:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Součásti rozdělit následujícím způsobem:

* značky (00 00)

* Délka klíče šifrování bloku (00 00-00 18)

* velikost bloku šifrování bloku (00 00-00 08)

* Délka klíče HMAC (00 00-00 14)

* ověřování algoritmem digest velikost HMAC (00 00-00 14)

* Bloková šifra výstup zásad replikace HESEL (AB B1 - E1 0E) a

* výstup HMAC PRF (76 EB - end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Režim Galois/čítač šifrování + ověřování

Hlavičku kontextu se skládá z následujících součástí:

* [16 bitů] Hodnota 00 01, což je značka znamená "GCM šifrování + ověřování".

* [32 bitů] Délka klíče (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku.

* [32 bitů] Její velikost (v bajtech, formát big-endian) používané během operace ověřeného šifrování. (Pro náš systém tento problém je vyřešený v nonce velikosti = 96 bits.)

* [32 bitů] Velikost bloku (v bajtech, formát big-endian) šifrovací algoritmus symetrického bloku. (Pro GCM, tento problém je vyřešený v velikosti bloku = 128 bitů.)

* [32 bitů] Ověřování tag velikost (v bajtech, formát big-endian) vytvořeného funkcí ověřené šifrování. (Pro náš systém tento problém je vyřešený v velikost značky = 128 bitů.)

* [128 bitů] Značka Enc_GCM (K_E nonce, ""), což je výstup šifrovací algoritmus symetrického bloku vzhledem zadání prázdného řetězce a kde hodnota nonce je 96bitový vektor všechny nulové.

K_E je odvozen použitím shodný mechanismus, stejně jako v CBC šifrování + HMAC ověřovacím scénáři. Ale protože zde není žádná K_H, v podstatě máme | K_H | = 0, a algoritmus Hroutí k níže uvedeného formuláře.

K_E = SP800_108_CTR (prf = HMACSHA512, key = "", label = "", kontext = "")

### <a name="example-aes-256-gcm"></a>Příklad: AES-256-GCM

Nejprve nechat K_E = SP800_108_CTR (prf = HMACSHA512, key = "", popisek = "", kontext = ""), kde | K_E | = 256 bitů.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

V dalším kroku compute ověřování značka Enc_GCM (K_E nonce, "") pro AES-256-GCM zadané hodnoty nonce = 096 a K_E jak je uvedeno výše.

výsledek: = E7DCCE66DF855A323A6BB7BD7A59BE45

Tímto se vytvoří hlavičku úplný kontext níže:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Součásti rozdělit následujícím způsobem:

* značky (00 01)

* Délka klíče šifrování bloku (00 00-00 20)

* její velikost (00 00 00 0 C)

* velikost bloku šifrování bloku (00 00-00 10)

* velikost značky ověřování (00 00-00 10) a

* ověřovací značka ve spuštění blokových šifrách (řadič domény E7 - end).
