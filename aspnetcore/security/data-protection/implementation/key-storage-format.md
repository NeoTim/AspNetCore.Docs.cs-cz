---
title: Formát ukládání klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace formát ukládání klíčů ochranu dat ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208015"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formát ukládání klíčů v ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Objekty jsou uložené neaktivní v reprezentaci XML. Výchozí adresář pro úložiště klíčů je % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>\<Klíč > – element

Kódů jako objektů nejvyšší úrovně v úložišti klíčů. Podle konvence klíče mají název souboru **key-{guid} .xml**, kde je identifikátor klíče {guid}. Každý takový soubor obsahuje jeden klíč. Formát souboru je následující.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

\<Klíč > prvek obsahuje následující atributy a podřízené prvky:

* Id klíče. Tato hodnota je zpracováván jako autoritativní; Název souboru je jednoduše nicety pro lidské čitelnost.

* Verze \<klíč > element, momentálně pevně nastavena na 1.

* Vytváření, aktivace a datum vypršení platnosti klíče.

* A \<popisovač > element, který obsahuje informace o používání ověřeného šifrování obsažené v rámci tohoto klíče.

V příkladu výše id klíče je {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, byla vytvořena a aktivovat 19. března 2015, a má dobu platnosti 90 dnů. (Někdy datum aktivace může být mírně před datum vytvoření jako v následujícím příkladu. Toto je z důvodu nit jak rozhraní API pro práci a je neškodný v praxi.)

## <a name="the-descriptor-element"></a>\<Popisovač > – element

Vnější \<popisovač > prvku obsahuje atribut deserializerType, což je název kvalifikovaný pro sestavení typu, který implementuje IAuthenticatedEncryptorDescriptorDeserializer. Tento typ je zodpovědný za čtení vnitřní \<popisovač > elementu a k analýze informací obsažených v rámci.

Konkrétní formát \<popisovač > element závisí na implementaci ověřeného šifrování zapouzdřené pomocí klíče a jednotlivých Převodník typů očekává, že trochu jiný formát pro toto. Obecně platí, ale tento element bude obsahovat vylepšením informace (názvy, typy, identifikátory OID, nebo podobné) a tajné klíče. V příkladu výše popisovač určuje, že zabalí tento klíč AES-256-CBC šifrování + ověřování HMACSHA256.

## <a name="the-encryptedsecret-element"></a>\<EncryptedSecret > – element

**&lt;EncryptedSecret&gt;** element, který obsahuje zašifrované formě tajného klíče může být k dispozici Pokud [je povolené šifrování tajných klíčů v klidovém stavu](xref:security/data-protection/implementation/key-encryption-at-rest). Atribut `decryptorType` je název kvalifikovaný pro sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Tento typ je zodpovědný za čtení vnitřní **&lt;encryptedKey&gt;** elementu a dešifruje je obnovit původní ve formátu prostého textu.

Stejně jako u `<descriptor>`, konkrétní formát `<encryptedSecret>` element závisí mechanismus šifrování v klidovém stavu používá. Ve výše uvedeném příkladu se hlavní klíč šifrují pomocí rozhraní Windows DPAPI za komentář.

## <a name="the-revocation-element"></a>\<Odvolání > – element

Zrušení existovat jako objektů nejvyšší úrovně v úložišti klíčů. Podle konvence mají řetězům název souboru **odvolání-{časové razítko} .xml** (pro odvolání všechny klíče do určitého data) nebo **odvolání-{guid} .xml** (pro odvolání určitého klíče). Každý soubor obsahuje jediný \<odvolání > element.

Pro zrušení jednotlivé klíče, bude obsah souboru níže.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

V takovém případě se zrušil pouze se zadaným klíčem. Pokud je id klíče "*", ale stejně jako v následujícím příkladu jsou všechny klíče, jejichž datum je před datem zadaným odvolání odvolán.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<Důvod > element se vůbec nečte v systému. Jednoduše praktické místo k uložení čitelné důvod odvolání je.
