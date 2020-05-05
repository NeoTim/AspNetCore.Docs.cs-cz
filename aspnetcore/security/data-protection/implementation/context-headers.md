---
title: Hlavičky kontextu v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core hlaviček kontextu ochrany dat.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 381cc137d1de87e87f36c3b32a6a551a318ed3cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776952"
---
# <a name="context-headers-in-aspnet-core"></a>Hlavičky kontextu v ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Pozadí a teorie

V systému ochrany dat označuje "klíč" objekt, který může poskytovat ověřené šifrovací služby. Každý klíč je identifikován jedinečným ID (identifikátor GUID) a obsahuje informace o algoritmu IT a Entropic materiál. Je vhodné, aby každý klíč měl jedinečnou entropii, ale systém ho nedokáže vyhovět, a potřebujeme také pro vývojáře, kteří můžou změnit klíčového prstence ručně úpravou algoritmových informací stávajícího klíče ve službě Key Ring. Aby bylo možné dosáhnout našich požadavků na zabezpečení, má systém ochrany dat koncept [kryptografické flexibility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), který umožňuje bezpečné použití jedné entropicové hodnoty napříč několika kryptografickými algoritmy.

Většina systémů, které podporují kryptografickou flexibilitu, tak, že zahrnuje některé identifikační informace o algoritmu uvnitř datové části. Identifikátor OID tohoto algoritmu je obecně dobrým kandidátem. Jedním z problémů, na které jsme narazili, je to, že existuje několik způsobů, jak zadat stejný algoritmus: "AES" (CNG) a spravované algoritmy AES, AesManaged, AesCryptoServiceProvider, AesCng a RijndaelManaged (zadané konkrétní parametry) jsou vlastně stejné a musíme udržovat mapování všech těchto objektů na správný identifikátor OID. Pokud vývojář chtěl poskytnout vlastní algoritmus (nebo dokonce i jinou implementaci AES!), musí nám sdělit svůj identifikátor OID. Tento dodatečný registrační krok usnadňuje konfiguraci systému, zejména bolestivý.

Krok zpět, rozhodli jsme se, že jsme se k problému přistupovali z nesprávného směru. Identifikátor OID oznamuje, co je to algoritmus, ale ve skutečnosti to nezáleží. Pokud musíme použít jednu Entropic hodnotu bezpečně ve dvou různých algoritmech, není nutné, aby bylo možné zjistit, jaké jsou algoritmy skutečně. To, co se ve skutečnosti zajímá, je způsob, jakým se chovají. Libovolný dát symetrický algoritmus šifry je také silný pseudonáhodných permutace (PRP): Opravte vstupy (klíč, režim zřetězení, IV, prostý text) a výstup šifrovaného textu se neshoduje s tím, že se pravděpodobnost zahlcení od všech ostatních algoritmů symetrického šifry, které mají stejné vstupy. Podobně platí, že jakákoli funkce dát s klíčem hash je také silnou pseudonáhodných funkcí (PRF) a předána pevně daná vstupní sada. její výstup bude bez jakýchkoli dalších funkcí hash s klíčem naprosto jedinečný.

Tento koncept silných PRPs a PRFs používáme k sestavení hlavičky kontextu. Tato hlavička kontextu v podstatě funguje jako stabilní kryptografický otisk přes používané algoritmy pro jakoukoli danou operaci a poskytuje kryptografickou flexibilitu, kterou vyžaduje systém ochrany dat. Tato hlavička je reprodukovatelná a používá se později jako součást [procesu odvození podklíče](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Existují dva různé způsoby, jak vytvořit hlavičku kontextu v závislosti na režimech fungování základních algoritmů.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Šifrování CBC a ověřování HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Záhlaví kontextu se skládá z následujících součástí:

* [16 bitů] Hodnota 00 00, což je označení "CBC Encryption + HMAC Authentication".

* [32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.

* [32 bitů] Velikost bloku (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.

* [32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu HMAC. (V současné době je velikost klíče vždy shodná s velikostí Digest.)

* [32 bitů] Velikost algoritmu Digest (v bajtech, Big Endian) algoritmu HMAC.

* EncCBC (K_E, IV, ""), což je výstup algoritmu symetrického šifrování bloku, který předaný prázdný řetězec vstupu a kde IV je nulový vektor. Konstrukce K_E je popsána níže.

* MAC (K_H, ""), což je výstup algoritmu HMAC, který předaný prázdný řetězec vstupu. Konstrukce K_H je popsána níže.

V ideálním případě můžeme všem nulovým vektorům předat K_E a K_H. Chceme se ale vyhnout situaci, kdy základní algoritmus kontroluje existenci slabých klíčů před provedením jakýchkoli operací (zejména DES a 3DES), což vylučuje použití jednoduchého nebo opakovaného vzoru, jako je nulový vektor.

Místo toho používáme NIST SP800-108 KDF v režimu počítadla (viz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) s klíčem s nulovou délkou, popiskem a kontextem a HMACSHA512 jako základní PRF. Odvozujeme | K_E | + | K_H | bajty výstupu a pak rozloží výsledek do K_E a K_H sám sebe. Matematicky je znázorněna následujícím způsobem.

(K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512; Key = ""; label = ""; Context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Příklad: AES-192-CBC + HMACSHA256

Příklad: Zvažte případ, kde algoritmus symetrického bloku šifry je AES-192-CBC a ověřovací algoritmus je HMACSHA256. Systém vygeneruje hlavičku kontextu pomocí následujících kroků.

First, let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512; Key = ""; label = ""; Context = ""); WHERE | K_E | = 192 bitů a | K_H | = 256 bitů na zadané algoritmy. To vede k K_E = 5BB6.. 21DD a K_H = A04A.. 00A9 v následujícím příkladu:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

V dalším kroku COMPUTE Enc_CBC (K_E, IV, "") pro AES-192-CBC podle IV = 0 * a K_E výše.

výsledek: = F474B1872B3B53E4721DE19C0841DB6F

V dalším kroku COMPUTE MAC (K_H,) pro HMACSHA256 K_H, jak je uvedeno výše.

výsledek: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Tím se vytvoří úplné záhlaví kontextu níže:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Toto kontextové záhlaví je kryptografický otisk páru ověřených šifrovacích algoritmů (AES-192-CBC Encryption + HMACSHA256 Validation). Komponenty, jak je popsáno [výše](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , jsou následující:

* Značka (00 00)

* délka šifrovacího klíče bloku (00 00 00 18)

* velikost bloku šifry bloku (00 00 00 10)

* Délka klíče HMAC (00 00 00 20)

* velikost Digest HMAC (00 00 00 20)

* výstup Block šifry PRP (F4 74-DB 6F) a

* výstup HMAC PRF (D4 79-end).

> [!NOTE]
> Záhlaví kontextu ověřování CBC + HMAC je tvořeno stejným způsobem bez ohledu na to, zda jsou implementace algoritmů poskytovány rozhraním Windows CNG nebo spravovanými typy SymmetricAlgorithm a KeyedHashAlgorithm. To umožňuje aplikacím běžícím na různých operačních systémech spolehlivě sestavovat stejné záhlaví kontextu, i když se implementace algoritmů liší od operačních systémech. (V praxi nemusí být KeyedHashAlgorithm správným HMAC. Může to být libovolný typ algoritmu hash s klíčem.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Příklad: 3DES-192-CBC + HMACSHA1

First, let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512; Key = ""; label = ""; Context = ""); WHERE | K_E | = 192 bitů a | K_H | = 160 bitů na zadané algoritmy. To vede k K_E = A219.. E2BB a K_H = DC4A.. B464 v následujícím příkladu:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

V dalším kroku COMPUTE Enc_CBC (K_E, IV, "") pro algoritmus 3DES-192-CBC, který má IV = 0 * a K_E výše.

výsledek: = ABB100F81E53E10E

V dalším kroku COMPUTE MAC (K_H,) pro HMACSHA1 K_H, jak je uvedeno výše.

výsledek: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Tím dojde k vytvoření úplného kontextu, který je kryptografickým otiskem ověřeného páru šifrovacího algoritmu (3DES-192-CBC šifrování + HMACSHA1 ověření), jak je uvedeno níže:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Komponenty se rozdělí takto:

* Značka (00 00)

* délka šifrovacího klíče bloku (00 00 00 18)

* velikost bloku šifry bloku (00 00 00 08)

* Délka klíče HMAC (00 00 00 14)

* velikost Digest HMAC (00 00 00 14)

* blok "Block šifry PRP – výstup (AB B1 – E1 0E) a

* výstup HMAC PRF (76 EB-end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/režim čítače šifrování + ověřování

Záhlaví kontextu se skládá z následujících součástí:

* [16 bitů] Hodnota 00 01, což je označení "GCM Encryption + Authentication".

* [32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.

* [32 bitů] Velikost hodnoty nonce (v bajtech, Big Endian), která se používá během ověřených šifrovacích operací. (Pro náš systém je to opraveno při velikosti nonce = 96 bitů.)

* [32 bitů] Velikost bloku (v bajtech, Big Endian) algoritmu symetrického bloku šifrování. (Pro GCM to je opraveno v bloku Size = 128 bitů.)

* [32 bitů] Velikost značky ověřování (v bajtech, Big Endian) vytvořeného pomocí ověřené šifrovací funkce. (Pro náš systém je to pevně nastavená na velikost značky = 128 bitů.)

* [128 bitů] Značka Enc_GCM (K_E, nonce, ""), což je výstup algoritmu symetrického šifrování bloku, který předali prázdný vstup řetězce a kde hodnota nonce je 96 celý vektor s nulovým počtem bitů.

K_E je odvozený pomocí stejného mechanismu jako ve scénáři ověřování CBC Encryption + HMAC. Vzhledem k tomu, že zde nejsou žádné K_H v hraní, máme v podstatě | K_H | = 0 a algoritmus se sbalí do níže uvedeného formuláře.

K_E = SP800_108_CTR (PRF = HMACSHA512; Key = ""; label = ""; Context = "")

### <a name="example-aes-256-gcm"></a>Příklad: AES-256-GCM

Nejdřív dejte K_E = SP800_108_CTR (PRF = HMACSHA512, Key = "", Label = "", Context = ""), kde | K_E | = 256 bitů.

K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Dále vypočítají ověřovací značku Enc_GCM (K_E, nonce, "") pro AES-256-GCM s daným znakem nonce = 096 a K_E, jak je uvedeno výše.

výsledek: = E7DCCE66DF855A323A6BB7BD7A59BE45

Tím se vytvoří úplné záhlaví kontextu níže:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Komponenty se rozdělí takto:

* Značka (00 01)

* délka šifrovacího klíče bloku (00 00 00 20)

* velikost hodnoty nonce (00 00 00 0C)

* velikost bloku šifry bloku (00 00 00 10)

* velikost značky ověřování (00 00 00 10) a

* Značka ověřování pro spuštění bloku Block šifry (E7 DC-end).
