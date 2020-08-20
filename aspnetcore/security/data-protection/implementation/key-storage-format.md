---
title: Formát úložiště klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci formátu úložiště klíčů pro ASP.NET Core Data Protection.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631989"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formát úložiště klíčů v ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Objekty jsou uloženy v klidovém vyjádření v XML. Výchozím adresářem pro úložiště klíčů je:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$Home/.ASPNET/DataProtection-Keys*

## <a name="the-key-element"></a>\<key>Element

Klíče existují jako objekty nejvyšší úrovně v úložišti klíčů. Podle klíčů konvence mají klíč názvu souboru **{GUID}. XML**, kde {GUID} je ID klíče. Každý takový soubor obsahuje jeden klíč. Formát souboru je následující.

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

\<key>Element obsahuje následující atributy a podřízené prvky:

* ID klíče Tato hodnota je považována za autoritativní; název souboru je jednoduše Nicety pro lidské čitelnost.

* Verze \<key> elementu, aktuálně opravena na 1.

* Datum vytvoření, aktivace a vypršení platnosti klíče.

* \<descriptor>Element, který obsahuje informace o ověřované implementaci šifrování obsažené v tomto klíči.

V předchozím příkladu je ID klíče {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, bylo vytvořeno a aktivováno 19. března 2015 a má dobu životnosti 90 dní. (Někdy může být datum aktivace mírně dřívější než datum vytvoření jako v tomto příkladu. Důvodem je puntičkářské, jak rozhraní API fungují a jsou v praxi neškodné.)

## <a name="the-descriptor-element"></a>\<descriptor>Element

Vnější \<descriptor> prvek obsahuje atribut deserializerType, který je kvalifikovaný název sestavení typu, který implementuje IAuthenticatedEncryptorDescriptorDeserializer. Tento typ zodpovídá za čtení vnitřního \<descriptor> prvku a pro analýzu informací obsažených v rámci.

Konkrétní formát \<descriptor> elementu závisí na implementaci ověřovaného šifry zapouzdřené klíčem a každý typ deserializace očekává mírně odlišný formát. Obecně platí, že tento prvek bude obsahovat informace o algoritmech (názvy, typy, OID nebo podobné) a materiál tajného klíče. V předchozím příkladu popisovač určuje, že tento klíč obtéká AES-256-CBC Encryption + HMACSHA256 ověření.

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret>Element

Pokud [je povolená šifrování tajných klíčů v klidovém](xref:security/data-protection/implementation/key-encryption-at-rest)stavu, může dojít k přítomnosti ** &lt; encryptedSecret &gt; ** elementu, který obsahuje šifrovaný tvar materiálu tajného klíče. Atribut `decryptorType` je kvalifikovaný název sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Tento typ zodpovídá za čtení vnitřního prvku ** &lt; EncryptedKey &gt; ** a jeho dešifrování za účelem obnovení původního prostého textu.

Stejně jako v systému `<descriptor>` závisí konkrétní formát `<encryptedSecret>` elementu na použití šifrovacího mechanismu standardu REST. V předchozím příkladu je hlavní klíč zašifrovaný pomocí rozhraní Windows DPAPI na komentář.

## <a name="the-revocation-element"></a>\<revocation>Element

Odvolání existují v úložišti klíčů jako objekty nejvyšší úrovně. Zrušením konvence mají odvolání názvu souboru **{timestamp}. XML** (pro odvolání všech klíčů před konkrétním datem) nebo **odvolání-{GUID}. XML** (pro odvolání konkrétního klíče). Každý soubor obsahuje jeden \<revocation> element.

U odvolání jednotlivých klíčů bude obsah souboru uvedený níže.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

V tomto případě se odvolá jenom zadaný klíč. Pokud je ID klíče "*", ale jak je uvedeno v následujícím příkladu, všechny klíče, jejichž datum vytvoření je před zadaným datem odvolání, jsou odvolány.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason>Element nikdy nepřečetl systém. Je to prostě vhodné místo pro ukládání snadno čitelných důvodů pro odvolání.
