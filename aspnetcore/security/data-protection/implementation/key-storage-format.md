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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="f4716-103">Formát úložiště klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4716-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="f4716-104">Objekty jsou uloženy v klidovém vyjádření v XML.</span><span class="sxs-lookup"><span data-stu-id="f4716-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="f4716-105">Výchozím adresářem pro úložiště klíčů je:</span><span class="sxs-lookup"><span data-stu-id="f4716-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="f4716-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="f4716-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="f4716-107">macOS/Linux: *$Home/.ASPNET/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="f4716-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="f4716-108">\<key>Element</span><span class="sxs-lookup"><span data-stu-id="f4716-108">The \<key> element</span></span>

<span data-ttu-id="f4716-109">Klíče existují jako objekty nejvyšší úrovně v úložišti klíčů.</span><span class="sxs-lookup"><span data-stu-id="f4716-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="f4716-110">Podle klíčů konvence mají klíč názvu souboru **{GUID}. XML**, kde {GUID} je ID klíče.</span><span class="sxs-lookup"><span data-stu-id="f4716-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="f4716-111">Každý takový soubor obsahuje jeden klíč.</span><span class="sxs-lookup"><span data-stu-id="f4716-111">Each such file contains a single key.</span></span> <span data-ttu-id="f4716-112">Formát souboru je následující.</span><span class="sxs-lookup"><span data-stu-id="f4716-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="f4716-113">\<key>Element obsahuje následující atributy a podřízené prvky:</span><span class="sxs-lookup"><span data-stu-id="f4716-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="f4716-114">ID klíče Tato hodnota je považována za autoritativní; název souboru je jednoduše Nicety pro lidské čitelnost.</span><span class="sxs-lookup"><span data-stu-id="f4716-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="f4716-115">Verze \<key> elementu, aktuálně opravena na 1.</span><span class="sxs-lookup"><span data-stu-id="f4716-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="f4716-116">Datum vytvoření, aktivace a vypršení platnosti klíče.</span><span class="sxs-lookup"><span data-stu-id="f4716-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="f4716-117">\<descriptor>Element, který obsahuje informace o ověřované implementaci šifrování obsažené v tomto klíči.</span><span class="sxs-lookup"><span data-stu-id="f4716-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="f4716-118">V předchozím příkladu je ID klíče {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, bylo vytvořeno a aktivováno 19. března 2015 a má dobu životnosti 90 dní.</span><span class="sxs-lookup"><span data-stu-id="f4716-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="f4716-119">(Někdy může být datum aktivace mírně dřívější než datum vytvoření jako v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="f4716-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="f4716-120">Důvodem je puntičkářské, jak rozhraní API fungují a jsou v praxi neškodné.)</span><span class="sxs-lookup"><span data-stu-id="f4716-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="f4716-121">\<descriptor>Element</span><span class="sxs-lookup"><span data-stu-id="f4716-121">The \<descriptor> element</span></span>

<span data-ttu-id="f4716-122">Vnější \<descriptor> prvek obsahuje atribut deserializerType, který je kvalifikovaný název sestavení typu, který implementuje IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="f4716-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="f4716-123">Tento typ zodpovídá za čtení vnitřního \<descriptor> prvku a pro analýzu informací obsažených v rámci.</span><span class="sxs-lookup"><span data-stu-id="f4716-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="f4716-124">Konkrétní formát \<descriptor> elementu závisí na implementaci ověřovaného šifry zapouzdřené klíčem a každý typ deserializace očekává mírně odlišný formát.</span><span class="sxs-lookup"><span data-stu-id="f4716-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="f4716-125">Obecně platí, že tento prvek bude obsahovat informace o algoritmech (názvy, typy, OID nebo podobné) a materiál tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="f4716-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="f4716-126">V předchozím příkladu popisovač určuje, že tento klíč obtéká AES-256-CBC Encryption + HMACSHA256 ověření.</span><span class="sxs-lookup"><span data-stu-id="f4716-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="f4716-127">\<encryptedSecret>Element</span><span class="sxs-lookup"><span data-stu-id="f4716-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="f4716-128">Pokud [je povolená šifrování tajných klíčů v klidovém](xref:security/data-protection/implementation/key-encryption-at-rest)stavu, může dojít k přítomnosti \*\* &lt; encryptedSecret &gt; \*\* elementu, který obsahuje šifrovaný tvar materiálu tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="f4716-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="f4716-129">Atribut `decryptorType` je kvalifikovaný název sestavení typu, který implementuje [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="f4716-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="f4716-130">Tento typ zodpovídá za čtení vnitřního prvku \*\* &lt; EncryptedKey &gt; \*\* a jeho dešifrování za účelem obnovení původního prostého textu.</span><span class="sxs-lookup"><span data-stu-id="f4716-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="f4716-131">Stejně jako v systému `<descriptor>` závisí konkrétní formát `<encryptedSecret>` elementu na použití šifrovacího mechanismu standardu REST.</span><span class="sxs-lookup"><span data-stu-id="f4716-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="f4716-132">V předchozím příkladu je hlavní klíč zašifrovaný pomocí rozhraní Windows DPAPI na komentář.</span><span class="sxs-lookup"><span data-stu-id="f4716-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="f4716-133">\<revocation>Element</span><span class="sxs-lookup"><span data-stu-id="f4716-133">The \<revocation> element</span></span>

<span data-ttu-id="f4716-134">Odvolání existují v úložišti klíčů jako objekty nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="f4716-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="f4716-135">Zrušením konvence mají odvolání názvu souboru **{timestamp}. XML** (pro odvolání všech klíčů před konkrétním datem) nebo **odvolání-{GUID}. XML** (pro odvolání konkrétního klíče).</span><span class="sxs-lookup"><span data-stu-id="f4716-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="f4716-136">Každý soubor obsahuje jeden \<revocation> element.</span><span class="sxs-lookup"><span data-stu-id="f4716-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="f4716-137">U odvolání jednotlivých klíčů bude obsah souboru uvedený níže.</span><span class="sxs-lookup"><span data-stu-id="f4716-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="f4716-138">V tomto případě se odvolá jenom zadaný klíč.</span><span class="sxs-lookup"><span data-stu-id="f4716-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="f4716-139">Pokud je ID klíče "\*", ale jak je uvedeno v následujícím příkladu, všechny klíče, jejichž datum vytvoření je před zadaným datem odvolání, jsou odvolány.</span><span class="sxs-lookup"><span data-stu-id="f4716-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="f4716-140">\<reason>Element nikdy nepřečetl systém.</span><span class="sxs-lookup"><span data-stu-id="f4716-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="f4716-141">Je to prostě vhodné místo pro ukládání snadno čitelných důvodů pro odvolání.</span><span class="sxs-lookup"><span data-stu-id="f4716-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
