---
title: Formát ukládání klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace formát ukládání klíčů ochranu dat ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903054"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="73fae-103">Formát ukládání klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73fae-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="73fae-104">Objekty jsou uložené neaktivní v reprezentaci XML.</span><span class="sxs-lookup"><span data-stu-id="73fae-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="73fae-105">Výchozí adresář pro úložiště klíčů je % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span><span class="sxs-lookup"><span data-stu-id="73fae-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="73fae-106">\<Klíč > – element</span><span class="sxs-lookup"><span data-stu-id="73fae-106">The \<key> element</span></span>

<span data-ttu-id="73fae-107">Kódů jako objektů nejvyšší úrovně v úložišti klíčů.</span><span class="sxs-lookup"><span data-stu-id="73fae-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="73fae-108">Podle konvence klíče mají název souboru **key-{guid} .xml**, kde je identifikátor klíče {guid}.</span><span class="sxs-lookup"><span data-stu-id="73fae-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="73fae-109">Každý takový soubor obsahuje jeden klíč.</span><span class="sxs-lookup"><span data-stu-id="73fae-109">Each such file contains a single key.</span></span> <span data-ttu-id="73fae-110">Formát souboru je následující.</span><span class="sxs-lookup"><span data-stu-id="73fae-110">The format of the file is as follows.</span></span>

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

<span data-ttu-id="73fae-111">\<Klíč > prvek obsahuje následující atributy a podřízené prvky:</span><span class="sxs-lookup"><span data-stu-id="73fae-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="73fae-112">Id klíče. Tato hodnota je zpracováván jako autoritativní; Název souboru je jednoduše nicety pro lidské čitelnost.</span><span class="sxs-lookup"><span data-stu-id="73fae-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="73fae-113">Verze \<klíč > element, momentálně pevně nastavena na 1.</span><span class="sxs-lookup"><span data-stu-id="73fae-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="73fae-114">Vytváření, aktivace a datum vypršení platnosti klíče.</span><span class="sxs-lookup"><span data-stu-id="73fae-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="73fae-115">A \<popisovač > element, který obsahuje informace o používání ověřeného šifrování obsažené v rámci tohoto klíče.</span><span class="sxs-lookup"><span data-stu-id="73fae-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="73fae-116">V příkladu výše id klíče je {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, byla vytvořena a aktivovat 19. března 2015, a má dobu platnosti 90 dnů.</span><span class="sxs-lookup"><span data-stu-id="73fae-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="73fae-117">(Někdy datum aktivace může být mírně před datum vytvoření jako v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="73fae-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="73fae-118">Toto je z důvodu nit jak rozhraní API pro práci a je neškodný v praxi.)</span><span class="sxs-lookup"><span data-stu-id="73fae-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="73fae-119">\<Popisovač > – element</span><span class="sxs-lookup"><span data-stu-id="73fae-119">The \<descriptor> element</span></span>

<span data-ttu-id="73fae-120">Vnější \<popisovač > prvku obsahuje atribut deserializerType, což je název kvalifikovaný pro sestavení typu, který implementuje IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="73fae-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="73fae-121">Tento typ je zodpovědný za čtení vnitřní \<popisovač > elementu a k analýze informací obsažených v rámci.</span><span class="sxs-lookup"><span data-stu-id="73fae-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="73fae-122">Konkrétní formát \<popisovač > element závisí na implementaci ověřeného šifrování zapouzdřené pomocí klíče a jednotlivých Převodník typů očekává, že trochu jiný formát pro toto.</span><span class="sxs-lookup"><span data-stu-id="73fae-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="73fae-123">Obecně platí, ale tento element bude obsahovat vylepšením informace (názvy, typy, identifikátory OID, nebo podobné) a tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="73fae-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="73fae-124">V příkladu výše popisovač určuje, že zabalí tento klíč AES-256-CBC šifrování + ověřování HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="73fae-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="73fae-125">\<EncryptedSecret > – element</span><span class="sxs-lookup"><span data-stu-id="73fae-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="73fae-126">**&lt;EncryptedSecret&gt;** element, který obsahuje zašifrované formě tajného klíče může být k dispozici Pokud [je povolené šifrování tajných klíčů v klidovém stavu](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="73fae-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="73fae-127">Atribut `decryptorType` je název kvalifikovaný pro sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="73fae-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="73fae-128">Tento typ je zodpovědný za čtení vnitřní **&lt;encryptedKey&gt;** elementu a dešifruje je obnovit původní ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="73fae-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="73fae-129">Stejně jako u `<descriptor>`, konkrétní formát `<encryptedSecret>` element závisí mechanismus šifrování v klidovém stavu používá.</span><span class="sxs-lookup"><span data-stu-id="73fae-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="73fae-130">Ve výše uvedeném příkladu se hlavní klíč šifrují pomocí rozhraní Windows DPAPI za komentář.</span><span class="sxs-lookup"><span data-stu-id="73fae-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="73fae-131">\<Odvolání > – element</span><span class="sxs-lookup"><span data-stu-id="73fae-131">The \<revocation> element</span></span>

<span data-ttu-id="73fae-132">Zrušení existovat jako objektů nejvyšší úrovně v úložišti klíčů.</span><span class="sxs-lookup"><span data-stu-id="73fae-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="73fae-133">Podle konvence mají řetězům název souboru **odvolání-{časové razítko} .xml** (pro odvolání všechny klíče do určitého data) nebo **odvolání-{guid} .xml** (pro odvolání určitého klíče).</span><span class="sxs-lookup"><span data-stu-id="73fae-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="73fae-134">Každý soubor obsahuje jediný \<odvolání > element.</span><span class="sxs-lookup"><span data-stu-id="73fae-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="73fae-135">Pro zrušení jednotlivé klíče, bude obsah souboru níže.</span><span class="sxs-lookup"><span data-stu-id="73fae-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="73fae-136">V takovém případě se zrušil pouze se zadaným klíčem.</span><span class="sxs-lookup"><span data-stu-id="73fae-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="73fae-137">Pokud je id klíče "\*", ale stejně jako v následujícím příkladu jsou všechny klíče, jejichž datum je před datem zadaným odvolání odvolán.</span><span class="sxs-lookup"><span data-stu-id="73fae-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="73fae-138">\<Důvod > element se vůbec nečte v systému.</span><span class="sxs-lookup"><span data-stu-id="73fae-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="73fae-139">Jednoduše praktické místo k uložení čitelné důvod odvolání je.</span><span class="sxs-lookup"><span data-stu-id="73fae-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
