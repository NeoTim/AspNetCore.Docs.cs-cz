---
title: Odvození podklíče a ověřované šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core odvození podklíče ochrany dat a ověřovaného šifrování.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: f373c37a5ea4dab91463d011d3ecd6799ae6d014
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408029"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Odvození podklíče a ověřované šifrování v ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

Většina klíčů ve službě Key Ring bude obsahovat určitou formu entropie a bude mít informace o algoritmech "CBC-Mode Encryption + HMAC Validation" nebo "GCM Encryption + Validation". V těchto případech odkazujeme na vloženou entropii jako na hlavní klíč (nebo KM) pro tento klíč a k odvození klíčů, které se budou používat pro skutečné kryptografické operace, provedeme funkci odvození klíče.

> [!NOTE]
> Klíče jsou abstraktní a vlastní implementace se nemusí chovat, jak je uvedeno níže. Pokud klíč poskytuje svou vlastní implementaci `IAuthenticatedEncryptor` namísto použití jednoho z našich vestavěných továren, mechanizmus popsaný v této části se už nepoužívá.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Další ověřená data a odvození podklíčů

`IAuthenticatedEncryptor`Rozhraní slouží jako základní rozhraní pro všechny ověřované operace šifrování. Jeho `Encrypt` Metoda přijímá dvě vyrovnávací paměti: prosté a additionalAuthenticatedData (AAD). Tok obsahu ve formátu prostého textu nemění volání `IDataProtector.Protect` , ale v systému je vygenerováno AAD a skládá se ze tří součástí:

1. 32 hlavička Magic F0 C9 F0, která identifikuje tuto verzi systému ochrany dat.

2. ID 128 klíče.

3. Řetězec s proměnlivou délkou tvořený řetězcem účelu, který vytvořil `IDataProtector` tuto operaci.

Vzhledem k tomu, že je AAD jedinečný pro řazenou kolekci členů všech tří součástí, můžeme ji použít k odvození nových klíčů od KM místo samotného použití KM ve všech našich kryptografických operacích. Pro každé volání metody `IAuthenticatedEncryptor.Encrypt` dojde k následujícímu procesu odvození klíče:

(K_E, K_H) = SP800_108_CTR_HMACSHA512 (K_M, AAD, contextHeader | | modifikátor písmen)

Tady zavoláme NIST SP800-108 KDF v režimu čítače (viz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) s následujícími parametry:

* Klíč odvození klíče (KDK) = K_M

* PRF = HMACSHA512

* Label = additionalAuthenticatedData

* kontext = contextHeader | | Modifikátor

Záhlaví kontextu má proměnlivou délku a v podstatě slouží jako kryptografický otisk algoritmů, pro které je odvozeno K_E a K_H. Klávesovým zkratem je 128 řetězec, který se náhodně generuje pro každé volání `Encrypt` a slouží k zajištění, že pravděpodobnost zahlcení, které pro a b, jsou pro tuto konkrétní operaci šifrování ověřování jedinečné, a to i v případě, že všechny ostatní vstupy KDF jsou konstantní.

Pro CBC režim šifrování + ověřování HMAC | K_E | je délka šifrovacího klíče symetrického bloku a | K_H | je velikost Digest rutiny HMAC. Pro GCM šifrování a operace ověřování | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Šifrování v režimu CBC a ověřování HMAC

Jakmile se K_E generuje prostřednictvím výše uvedeného mechanismu, vygenerujeme náhodný vektor inicializace a spustíme algoritmus symetrického šifrování bloku, který zakódování prostý text. Inicializační vektor a šifrovaný text se pak spustí pomocí rutiny HMAC inicializované s klíčem, který se K_H, aby se vytvořila adresa MAC. Tento proces a návratová hodnota jsou znázorněny na obrázku níže.

![Proces a návrat v CBC režimu](subkeyderivation/_static/cbcprocess.png)

*výstup: = modifikátor IV | | E_cbc (K_E, IV, data) | | HMAC (K_H, IV | | E_cbc (K_E, IV, data))*

> [!NOTE]
> `IDataProtector.Protect`Implementace před návratem do volajícího předřadí [hlavičku Magic a ID klíče](xref:security/data-protection/implementation/authenticated-encryption-details) do výstupu. Vzhledem k tomu, že hlavičky Magic a ID klíče jsou implicitně součástí [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), a protože Modifikátor klíče je jako vstup podáván do KDF, znamená to, že Mac ověřuje každý jednotlivý bajt konečné vrácené datové části.

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/režim čítače šifrování + ověřování

Jakmile se K_E generuje prostřednictvím výše uvedeného mechanismu, vygenerujeme náhodnou 96nou hodnotu nonce a spustíte algoritmus symetrického šifrování bloku, který zakódování prostý text a vytvoří 128 značku ověřování.

![Proces a návrat v GCM režimu](subkeyderivation/_static/galoisprocess.png)

*výstup: = modifikátor hodnota nonce | | E_gcm (K_E, nonce, data) | | authTag*

> [!NOTE]
> I když GCM nativně podporuje koncept AAD, pořád ještě neposkytujeme do původního KDFu AAD, která se zapojuje k předání prázdného řetězce do GCM pro svůj parametr AAD. Důvodem je dvojí přeložení. Nejprve [pro podporu flexibility, které](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) nikdy nechcete používat K_M přímo jako šifrovací klíč. Kromě toho GCM ukládá velmi přísné požadavky jedinečnosti na své vstupy. Pravděpodobnost, že se rutina šifrování GCM někdy vyvolala na dvou nebo více různých sadách vstupních dat se stejnou dvojicí (Key, nonce), nesmí překročit 2 ^ 32. Pokud opravujeme K_E nemůžeme provést více než 2 ^ 32 operací šifrování předtím, než spustíte afoul limitu 2 ^-32. Může se zdát, že se jedná o velmi velký počet operací, ale webový server s vysokým provozem může projít 4 000 000 000 požadavky v pouhých dnech, a to i v normálním trvání těchto klíčů. Aby zůstalo v souladu s limitem 2 ^-32, používáme modifikátor 128 bitového klíče a 96-bit nonce, který odpíná počet použitelných operací pro všechny dané K_M. Pro jednoduchost návrhu sdílíme cestu kódu KDF mezi CBC a GCM operacemi a vzhledem k tomu, že AAD je už v KDF zvážené, není potřeba je přesměrovat do rutiny GCM.
