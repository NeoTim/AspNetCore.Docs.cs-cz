---
title: Odvozování podklíčů a ověřené šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ochrany dat ASP.NET Core podklíče odvození a ověřené šifrování.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 37e7b01700e8a6b755b5ed16a9d7d75a9eeb970e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898434"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Odvozování podklíčů a ověřené šifrování v ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

Většina klíčů v aktualizační kanál, který klíč bude obsahovat určitou formu entropie a budou mít vylepšením informace s oznámením "režimu CBC šifrování + ověřování HMAC" nebo "GCM šifrování + ověřování". V těchto případech označujeme vložený entropie materiál hlavního klíče (nebo KM) pro tento klíč a provádíme funkce odvození klíče k odvození klíče, které se použije pro vlastní kryptografické operace.

> [!NOTE]
> Klíče jsou abstraktní a jak je uvedeno níže nemusí chovat vlastní implementaci. Pokud klíč poskytuje vlastní implementaci `IAuthenticatedEncryptor` místo pomocí jedné z našich předdefinované objekty pro vytváření, mechanismus popsané v této části už neplatí.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Odvozování podklíčů a další ověření dat

`IAuthenticatedEncryptor` Rozhraní slouží jako základní rozhraní pro všechny operace ověřené šifrování. Jeho `Encrypt` metoda má dvě vyrovnávací paměti: ve formátu prostého textu a additionalAuthenticatedData (AAD). Tok obsah ve formátu prostého textu beze změny volání `IDataProtector.Protect`, ale AAD je generováno systémem a se skládá ze tří součástí:

1. Magic hlavička pro 32bitovou verzi 09 F0 F0 C9, identifikující tuto verzi systému ochrany dat.

2. Id klíče 128 bitů.

3. Řetězec s proměnnou délkou vytvořený z řetězu účel, který vytvořili `IDataProtector` , který je provedením této operace.

Protože AAD je jedinečný pro n-tice všechny tři komponenty, můžeme je použít k odvození nové klíče z KM namísto použití KM samotné ve všech našich kryptografických operací. Pro všechna volání `IAuthenticatedEncryptor.Encrypt`, proběhne následující proces odvození klíče:

(K_E, K_H) = SP800_108_CTR_HMACSHA512 (contextHeader K_M AAD, || keyModifier)

Tady, voláme KDF SP800 108 NIST v režimu čítače (naleznete v tématu [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), s. 5.1) s následujícími parametry:

* Odvození klíče key (KDK) = K_M

* PRF = HMACSHA512

* label = additionalAuthenticatedData

* context = contextHeader || keyModifier

Hlavičku kontextu má proměnlivou délku a v podstatě slouží jako kryptografický otisk algoritmy, které jsme už odvozování K_E a K_H. Modifikátor klíče je řetězec 128bitové náhodně vygenerovaný pro každé volání `Encrypt` a slouží k zajištění s zahlcení pravděpodobnost, že KE a KH jsou jedinečné pro tuto operaci šifrování konkrétní ověřování i v případě, že všechny ostatní vstupy do KDF je konstantní.

Pro šifrování v režimu CBC + HMAC operace ověřování | K_E | Délka bloku symetrický šifrovací klíč, a | K_H | je velikost digest HMAC rutiny. Gcm – šifrování + ověřování operace | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Režimu CBC šifrování + ověřování HMAC

Jakmile K_E je generován prostřednictvím mechanismu výše, jsme generování náhodných inicializační vektor a spuštění šifrovací algoritmus symetrického bloku k zakódování jako prostý text. Inicializační vektor a šifrovaného textu se spusťte pomocí rutiny HMAC inicializován s klíčem K_H vytvoří počítači MAC. Tento proces a návratová hodnota je reprezentován graficky níže.

![Proces v režimu CBC a návrat](subkeyderivation/_static/cbcprocess.png)

*výstup: = keyModifier || vektor IV || E_cbc (data K_E, iv) || Metoda HMAC (K_H, iv || E_cbc (data K_E, iv))*

> [!NOTE]
> `IDataProtector.Protect` Bude implementace [předřaďte magic záhlaví a id klíče](xref:security/data-protection/implementation/authenticated-encryption-details) do výstupu před vrácením řízení volajícímu. Protože jsou implicitně magic záhlaví a id klíče součástí [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), a protože modifikátorem klíče se zobrazí jako vstup KDF, to znamená, že každý jeden bajt vrácená velikost datové části je ověřována MAC.

## <a name="galoiscounter-mode-encryption--validation"></a>Režim Galois/čítač šifrování + ověřování

Jakmile K_E je generován prostřednictvím mechanismu výše, jsme generovat náhodná hodnota nonce 96 bitů a spusťte šifrovací algoritmus symetrického bloku k zakódování jako prostý text a vytvoření 128bitové ověřovací značka.

![Proces režimu GCM a návrat](subkeyderivation/_static/galoisprocess.png)

*výstup: = keyModifier || Hodnota Nonce || E_gcm (K_E, nonce, data) || authTag*

> [!NOTE]
> Přestože GCM nativně podporuje koncept AAD, jsme se stále tak AAD jenom pro původní KDF vyjádří má být předán prázdný řetězec GCM pro její parametr AAD. Důvod pro to má dvě fáze. Nejprve je potřeba [pro podporu flexibilitu](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) jsme nikdy nechcete používat K_M přímo jako šifrovací klíč. Kromě toho GCM žáků velmi přísná jedinečnost jeho vstupů. Pravděpodobnost, že rutina šifrování GCM někdy vyvolané na dvou nebo více různých sad vstupních dat se stejným (klíče, nonce) pár nesmí být delší než 2 ^ 32. Pokud opravíme K_E jsme nemůžete provést více než 2 ^ 32 operace šifrování, než jsme spustit afoul o 2 ^ omezit -32. To může jevit jako velmi velký počet operací, ale vysokým provozem webový server můžete projít 4 miliard žádostí za pouhé dní, i v rámci normálního dobu života těchto klíčů. Chcete-li i nadále 2 ^ omezení pravděpodobnosti-32, Pokračujeme použít modifikátor klíče 128 bitů a hodnota nonce 96-bit, který výrazně přesahuje počet operací použitelné pro jakékoli dané K_M. Pro zjednodušení návrhu sdílíme KDF cesta kódu mezi operacemi CBC a GCM a vzhledem k tomu, KDF již náleží AAD není nutné předat rutině GCM.
