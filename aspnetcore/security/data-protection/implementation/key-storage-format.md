---
title: Formát úložiště klíčů v ASP.NET core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ASP.NET formátu úložiště klíčů Core Data Protection.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976934"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formát úložiště klíčů v ASP.NET core

<a name="data-protection-implementation-key-storage-format"></a>

Objekty jsou uloženy v klidovém stavu v reprezentaci XML. Výchozí adresář pro úložiště klíčů je:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>Klíčovým \<> prvkem

Klíče existují jako objekty nejvyšší úrovně v úložišti klíčů. Podle konvence klíče mají **název_souboru-{guid}.xml**, kde {guid} je id klíče. Každý takový soubor obsahuje jeden klíč. Formát souboru je následující.

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

Prvek \<> obsahuje následující atributy a podřízené prvky:

* Id klíče. Tato hodnota je považována za směrodatnou; název souboru je prostě jemnost pro lidskou čitelnost.

* Verze klíčového \<prvku>, aktuálně opravená na 1.

* Data vytvoření, aktivace a vypršení platnosti klíče.

* Deskriptor \<> prvek, který obsahuje informace o implementaci ověřeného šifrování obsažené v tomto klíči.

Ve výše uvedeném příkladu je id klíče {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, byl vytvořen a aktivován 19. (V některých obdobích může být datum aktivace mírně před datem vytvoření, jak je tomu v tomto příkladu. To je způsobeno nit v tom, jak api práce a je neškodný v praxi.)

## <a name="the-descriptor-element"></a>Prvek \<deskriptoru>

Vnější \<deskriptor> prvek obsahuje atribut deserializerType, což je název s kvalifikací sestavení typu, který implementuje iAuthenticatedEncryptorDescriptorDeserializer. Tento typ je zodpovědný \<za čtení vnitřní popisovač> prvek a pro analýzu informací obsažených v.

Konkrétní formát deskriptoru \<> prvek závisí na ověřené implementaci šifrátoru zapouzdřené klíčem a každý typ deserializátoru očekává mírně odlišný formát pro tento. Obecně však tento prvek bude obsahovat algoritmické informace (názvy, typy, OID nebo podobné) a tajný materiál klíče. Ve výše uvedeném příkladu deskriptor určuje, že tento klíč zabalí šifrování AES-256-CBC + ověření HMACSHA256.

## <a name="the-encryptedsecret-element"></a>Prvek \<encryptedSecret>

** &lt;Šifrovaný&gt; prvek,** který obsahuje šifrovanou formu materiálu tajného klíče, může být přítomen, pokud [je povoleno šifrování tajných kódů v klidovém stavu](xref:security/data-protection/implementation/key-encryption-at-rest). Atribut `decryptorType` je název s kvalifikací sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Tento typ je zodpovědný za čtení vnitřní ** &lt;encryptedKey&gt; ** element a dešifrování obnovit původní prostý text.

Stejně `<descriptor>`jako u , `<encryptedSecret>` konkrétní formát prvku závisí na mechanismu šifrování v klidovém stavu v provozu. Ve výše uvedeném příkladu je hlavní klíč zašifrován pomocí systému Windows DPAPI za komentář.

## <a name="the-revocation-element"></a>Prvek \<> zrušení

Odvolání existují jako objekty nejvyšší úrovně v úložišti klíčů. Podle konvence odvolání mají název souboru **odvolání-{časové razítko}.xml** (pro zrušení všech klíčů před určitým datem) nebo **odvolání-{guid}.xml** (pro zrušení určitého klíče). Každý soubor obsahuje \<jeden prvek> odvolání.

Pro odvolání jednotlivých klíčů bude obsah souboru následující.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

V tomto případě je odvolán pouze zadaný klíč. Pokud je id klíče "*", ale jako v níže uvedeném příkladu, jsou odvolány všechny klíče, jejichž datum vytvoření je před zadaným datem odvolání.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Důvod, \<proč> prvek je nikdy číst systémem. Je to prostě vhodné místo pro uložení člověkem čitelného důvodu pro odvolání.
