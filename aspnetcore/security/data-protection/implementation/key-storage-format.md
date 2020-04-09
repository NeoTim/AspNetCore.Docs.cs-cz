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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="a9a18-103">Formát úložiště klíčů v ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="a9a18-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="a9a18-104">Objekty jsou uloženy v klidovém stavu v reprezentaci XML.</span><span class="sxs-lookup"><span data-stu-id="a9a18-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="a9a18-105">Výchozí adresář pro úložiště klíčů je:</span><span class="sxs-lookup"><span data-stu-id="a9a18-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="a9a18-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="a9a18-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="a9a18-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="a9a18-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="a9a18-108">Klíčovým \<> prvkem</span><span class="sxs-lookup"><span data-stu-id="a9a18-108">The \<key> element</span></span>

<span data-ttu-id="a9a18-109">Klíče existují jako objekty nejvyšší úrovně v úložišti klíčů.</span><span class="sxs-lookup"><span data-stu-id="a9a18-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="a9a18-110">Podle konvence klíče mají **název_souboru-{guid}.xml**, kde {guid} je id klíče.</span><span class="sxs-lookup"><span data-stu-id="a9a18-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="a9a18-111">Každý takový soubor obsahuje jeden klíč.</span><span class="sxs-lookup"><span data-stu-id="a9a18-111">Each such file contains a single key.</span></span> <span data-ttu-id="a9a18-112">Formát souboru je následující.</span><span class="sxs-lookup"><span data-stu-id="a9a18-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="a9a18-113">Prvek \<> obsahuje následující atributy a podřízené prvky:</span><span class="sxs-lookup"><span data-stu-id="a9a18-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="a9a18-114">Id klíče. Tato hodnota je považována za směrodatnou; název souboru je prostě jemnost pro lidskou čitelnost.</span><span class="sxs-lookup"><span data-stu-id="a9a18-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="a9a18-115">Verze klíčového \<prvku>, aktuálně opravená na 1.</span><span class="sxs-lookup"><span data-stu-id="a9a18-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="a9a18-116">Data vytvoření, aktivace a vypršení platnosti klíče.</span><span class="sxs-lookup"><span data-stu-id="a9a18-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="a9a18-117">Deskriptor \<> prvek, který obsahuje informace o implementaci ověřeného šifrování obsažené v tomto klíči.</span><span class="sxs-lookup"><span data-stu-id="a9a18-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="a9a18-118">Ve výše uvedeném příkladu je id klíče {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, byl vytvořen a aktivován 19.</span><span class="sxs-lookup"><span data-stu-id="a9a18-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="a9a18-119">(V některých obdobích může být datum aktivace mírně před datem vytvoření, jak je tomu v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="a9a18-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="a9a18-120">To je způsobeno nit v tom, jak api práce a je neškodný v praxi.)</span><span class="sxs-lookup"><span data-stu-id="a9a18-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="a9a18-121">Prvek \<deskriptoru></span><span class="sxs-lookup"><span data-stu-id="a9a18-121">The \<descriptor> element</span></span>

<span data-ttu-id="a9a18-122">Vnější \<deskriptor> prvek obsahuje atribut deserializerType, což je název s kvalifikací sestavení typu, který implementuje iAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="a9a18-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="a9a18-123">Tento typ je zodpovědný \<za čtení vnitřní popisovač> prvek a pro analýzu informací obsažených v.</span><span class="sxs-lookup"><span data-stu-id="a9a18-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="a9a18-124">Konkrétní formát deskriptoru \<> prvek závisí na ověřené implementaci šifrátoru zapouzdřené klíčem a každý typ deserializátoru očekává mírně odlišný formát pro tento.</span><span class="sxs-lookup"><span data-stu-id="a9a18-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="a9a18-125">Obecně však tento prvek bude obsahovat algoritmické informace (názvy, typy, OID nebo podobné) a tajný materiál klíče.</span><span class="sxs-lookup"><span data-stu-id="a9a18-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="a9a18-126">Ve výše uvedeném příkladu deskriptor určuje, že tento klíč zabalí šifrování AES-256-CBC + ověření HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="a9a18-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="a9a18-127">Prvek \<encryptedSecret></span><span class="sxs-lookup"><span data-stu-id="a9a18-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="a9a18-128">\*\* &lt;Šifrovaný&gt; prvek,\*\* který obsahuje šifrovanou formu materiálu tajného klíče, může být přítomen, pokud [je povoleno šifrování tajných kódů v klidovém stavu](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="a9a18-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="a9a18-129">Atribut `decryptorType` je název s kvalifikací sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="a9a18-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="a9a18-130">Tento typ je zodpovědný za čtení vnitřní \*\* &lt;encryptedKey&gt; \*\* element a dešifrování obnovit původní prostý text.</span><span class="sxs-lookup"><span data-stu-id="a9a18-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="a9a18-131">Stejně `<descriptor>`jako u , `<encryptedSecret>` konkrétní formát prvku závisí na mechanismu šifrování v klidovém stavu v provozu.</span><span class="sxs-lookup"><span data-stu-id="a9a18-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="a9a18-132">Ve výše uvedeném příkladu je hlavní klíč zašifrován pomocí systému Windows DPAPI za komentář.</span><span class="sxs-lookup"><span data-stu-id="a9a18-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="a9a18-133">Prvek \<> zrušení</span><span class="sxs-lookup"><span data-stu-id="a9a18-133">The \<revocation> element</span></span>

<span data-ttu-id="a9a18-134">Odvolání existují jako objekty nejvyšší úrovně v úložišti klíčů.</span><span class="sxs-lookup"><span data-stu-id="a9a18-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="a9a18-135">Podle konvence odvolání mají název souboru **odvolání-{časové razítko}.xml** (pro zrušení všech klíčů před určitým datem) nebo **odvolání-{guid}.xml** (pro zrušení určitého klíče).</span><span class="sxs-lookup"><span data-stu-id="a9a18-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="a9a18-136">Každý soubor obsahuje \<jeden prvek> odvolání.</span><span class="sxs-lookup"><span data-stu-id="a9a18-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="a9a18-137">Pro odvolání jednotlivých klíčů bude obsah souboru následující.</span><span class="sxs-lookup"><span data-stu-id="a9a18-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="a9a18-138">V tomto případě je odvolán pouze zadaný klíč.</span><span class="sxs-lookup"><span data-stu-id="a9a18-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="a9a18-139">Pokud je id klíče "\*", ale jako v níže uvedeném příkladu, jsou odvolány všechny klíče, jejichž datum vytvoření je před zadaným datem odvolání.</span><span class="sxs-lookup"><span data-stu-id="a9a18-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="a9a18-140">Důvod, \<proč> prvek je nikdy číst systémem.</span><span class="sxs-lookup"><span data-stu-id="a9a18-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="a9a18-141">Je to prostě vhodné místo pro uložení člověkem čitelného důvodu pro odvolání.</span><span class="sxs-lookup"><span data-stu-id="a9a18-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
