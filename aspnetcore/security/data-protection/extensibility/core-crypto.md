---
title: Rozšiřitelnost základního kryptografie v ASP.NET Core
author: rick-anderson
description: Seznamte se s IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer a továrnou nejvyšší úrovně.
ms.author: riande
ms.date: 08/11/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: de34968f21eec28cf375ee9f75d3cb8b212c7e70
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404272"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="a69b8-103">Rozšiřitelnost základního kryptografie v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a69b8-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="a69b8-104">Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.</span><span class="sxs-lookup"><span data-stu-id="a69b8-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="a69b8-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a69b8-106">Rozhraní **IAuthenticatedEncryptor** je základním stavebním blokem kryptografického subsystému.</span><span class="sxs-lookup"><span data-stu-id="a69b8-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="a69b8-107">Na klíč je všeobecně jedna IAuthenticatedEncryptor a instance IAuthenticatedEncryptor balí všechny materiály kryptografického klíče a algoritmy, které jsou nezbytné k provádění kryptografických operací.</span><span class="sxs-lookup"><span data-stu-id="a69b8-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="a69b8-108">Vzhledem k tomu, že název navrhuje, zodpovídá za poskytování ověřených šifrovacích a dešifrovacích služeb.</span><span class="sxs-lookup"><span data-stu-id="a69b8-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="a69b8-109">Zpřístupňuje následující dvě rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a69b8-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="a69b8-110">Metoda Encrypt vrátí objekt blob, který obsahuje enciphered prostý text a ověřovací značku.</span><span class="sxs-lookup"><span data-stu-id="a69b8-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="a69b8-111">Ověřovací značka musí zahrnovat další ověřená data (AAD), i když se samotný AAD nemusí z poslední datové části zotavit.</span><span class="sxs-lookup"><span data-stu-id="a69b8-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="a69b8-112">Metoda dešifrer ověří ověřovací značku a vrátí dešifrovanou datovou část.</span><span class="sxs-lookup"><span data-stu-id="a69b8-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="a69b8-113">Všechny chyby (s výjimkou ArgumentNullException a podobných) by měly být homogenní na CryptographicException –.</span><span class="sxs-lookup"><span data-stu-id="a69b8-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="a69b8-114">Samotná instance IAuthenticatedEncryptor ve skutečnosti nemusí obsahovat klíč klíče.</span><span class="sxs-lookup"><span data-stu-id="a69b8-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="a69b8-115">Například implementace by mohla delegovat na modul HARDWAROVÉho zabezpečení pro všechny operace.</span><span class="sxs-lookup"><span data-stu-id="a69b8-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="a69b8-116">Vytvoření IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a69b8-117">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a69b8-118">Rozhraní **IAuthenticatedEncryptorFactory** představuje typ, který ví, jak vytvořit instanci [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="a69b8-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a69b8-119">Jeho rozhraní API je následující.</span><span class="sxs-lookup"><span data-stu-id="a69b8-119">Its API is as follows.</span></span>

* <span data-ttu-id="a69b8-120">CreateEncryptorInstance (klíč IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a69b8-121">U všech daných instancí IKey by měly být všechny ověřené šifry vytvořené jeho metodou CreateEncryptorInstance považovány za ekvivalentní, jako v níže uvedené ukázce kódu.</span><span class="sxs-lookup"><span data-stu-id="a69b8-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a69b8-122">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a69b8-123">Rozhraní **IAuthenticatedEncryptorDescriptor** představuje typ, který ví, jak vytvořit instanci [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="a69b8-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a69b8-124">Jeho rozhraní API je následující.</span><span class="sxs-lookup"><span data-stu-id="a69b8-124">Its API is as follows.</span></span>

* <span data-ttu-id="a69b8-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="a69b8-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a69b8-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="a69b8-127">Podobně jako IAuthenticatedEncryptor se předpokládá, že instance IAuthenticatedEncryptorDescriptor zabalí jeden konkrétní klíč.</span><span class="sxs-lookup"><span data-stu-id="a69b8-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="a69b8-128">To znamená, že u všech daných instancí IAuthenticatedEncryptorDescriptor by měly být všechny ověřené šifry vytvořené jeho metodou CreateEncryptorInstance považovány za ekvivalentní, jako v níže uvedené ukázce kódu.</span><span class="sxs-lookup"><span data-stu-id="a69b8-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="a69b8-129">IAuthenticatedEncryptorDescriptor (pouze ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="a69b8-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a69b8-130">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a69b8-131">Rozhraní **IAuthenticatedEncryptorDescriptor** představuje typ, který ví, jak se má exportovat do XML.</span><span class="sxs-lookup"><span data-stu-id="a69b8-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="a69b8-132">Jeho rozhraní API je následující.</span><span class="sxs-lookup"><span data-stu-id="a69b8-132">Its API is as follows.</span></span>

* <span data-ttu-id="a69b8-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a69b8-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a69b8-134">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="a69b8-135">Serializace XML</span><span class="sxs-lookup"><span data-stu-id="a69b8-135">XML Serialization</span></span>

<span data-ttu-id="a69b8-136">Hlavním rozdílem mezi IAuthenticatedEncryptor a IAuthenticatedEncryptorDescriptor je, že popisovač ví, jak vytvořit modul pro šifrování a dodat jej s platnými argumenty.</span><span class="sxs-lookup"><span data-stu-id="a69b8-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="a69b8-137">Vezměte v úvahu IAuthenticatedEncryptor, jehož implementace spoléhá na SymmetricAlgorithm a KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="a69b8-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="a69b8-138">Úloha zašifry je určena ke využívání těchto typů, ale nemusí nutně znát, odkud pocházejí tyto typy, takže nemůže skutečně napsat řádný popis způsobu, jak je znovu vytvořit, pokud se aplikace restartuje.</span><span class="sxs-lookup"><span data-stu-id="a69b8-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="a69b8-139">Popisovač funguje jako vyšší úroveň nad tímto.</span><span class="sxs-lookup"><span data-stu-id="a69b8-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="a69b8-140">Vzhledem k tomu, že popisovač ví, jak vytvořit instanci služby šifry (například ví, jak vytvořit požadované algoritmy), může toto znalosti ve formuláři XML serializovat, aby se instance šifry mohla znovu vytvořit po resetování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a69b8-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="a69b8-141">Popisovač lze serializovat prostřednictvím jeho rutiny ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="a69b8-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="a69b8-142">Tato rutina vrátí XmlSerializedDescriptorInfo, který obsahuje dvě vlastnosti: XElement reprezentace popisovače a typ, který představuje [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , který lze použít k obnovení tohoto deskriptoru, který má přidělen odpovídající XElement.</span><span class="sxs-lookup"><span data-stu-id="a69b8-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="a69b8-143">Serializovaný deskriptor může obsahovat citlivé informace, jako je například materiál kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="a69b8-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="a69b8-144">Systém ochrany dat má integrovanou podporu pro šifrování informací předtím, než je trvale uložený do úložiště.</span><span class="sxs-lookup"><span data-stu-id="a69b8-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="a69b8-145">Chcete-li tuto funkci využít, deskriptor by měl označit element, který obsahuje citlivé informace s názvem atributu "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), hodnotou "true".</span><span class="sxs-lookup"><span data-stu-id="a69b8-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="a69b8-146">Pro nastavení tohoto atributu je k dispozici pomocné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a69b8-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="a69b8-147">Zavolejte metodu rozšíření XElement. MarkAsRequiresEncryption () umístěnou v oboru názvů Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="a69b8-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="a69b8-148">Mohou existovat také případy, kdy serializovaný popisovač neobsahuje citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="a69b8-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="a69b8-149">Znovu zvažte případ kryptografického klíče uloženého v modulu hardwarového zabezpečení (HSM).</span><span class="sxs-lookup"><span data-stu-id="a69b8-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="a69b8-150">Deskriptor nemůže zapsat klíč klíče při serializaci sebe sama, protože modul HARDWAROVÉho zabezpečení nezveřejňuje materiál ve formě prostého textu.</span><span class="sxs-lookup"><span data-stu-id="a69b8-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="a69b8-151">Místo toho může popisovač zapsat verzi klíče zabaleného klíče (Pokud modul HARDWAROVÉho zabezpečení umožňuje export tímto způsobem) nebo vlastní jedinečný identifikátor HSM pro klíč.</span><span class="sxs-lookup"><span data-stu-id="a69b8-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="a69b8-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="a69b8-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="a69b8-153">Rozhraní **IAuthenticatedEncryptorDescriptorDeserializer** představuje typ, který ví, jak rekonstruovat instanci IAuthenticatedEncryptorDescriptor z XElement.</span><span class="sxs-lookup"><span data-stu-id="a69b8-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="a69b8-154">Zpřístupňuje jednu metodu:</span><span class="sxs-lookup"><span data-stu-id="a69b8-154">It exposes a single method:</span></span>

* <span data-ttu-id="a69b8-155">ImportFromXml (XElement element): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a69b8-156">Metoda ImportFromXml přijímá XElement vrácenou funkcí [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) a vytvoří ekvivalent původní IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="a69b8-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="a69b8-157">Typy, které implementují IAuthenticatedEncryptorDescriptorDeserializer, by měly mít jeden z následujících dvou veřejných konstruktorů:</span><span class="sxs-lookup"><span data-stu-id="a69b8-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="a69b8-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="a69b8-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="a69b8-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="a69b8-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="a69b8-160">Objekt IServiceProvider předaný konstruktoru může mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="a69b8-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="a69b8-161">Továrna nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="a69b8-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="a69b8-162">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a69b8-163">Třída **AlgorithmConfiguration** představuje typ, který ví, jak vytvořit instance [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="a69b8-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a69b8-164">Zpřístupňuje jedno rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a69b8-164">It exposes a single API.</span></span>

* <span data-ttu-id="a69b8-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a69b8-166">AlgorithmConfiguration se považujte za továrnu nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="a69b8-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="a69b8-167">Konfigurace slouží jako šablona.</span><span class="sxs-lookup"><span data-stu-id="a69b8-167">The configuration serves as a template.</span></span> <span data-ttu-id="a69b8-168">Zalomí informace o algoritmech (např. Tato konfigurace vytvoří popisovače pomocí GCM hlavního klíče AES-128), ale ještě není přidružená k určitému klíči.</span><span class="sxs-lookup"><span data-stu-id="a69b8-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a69b8-169">Když se zavolá CreateNewDescriptor, vytvoří se nový klíčový materiál výhradně pro toto volání a vytvoří se nový IAuthenticatedEncryptorDescriptor, který zabalí tento klíčový materiál a algoritmy, které jsou nutné k využívání materiálu.</span><span class="sxs-lookup"><span data-stu-id="a69b8-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a69b8-170">Klíčový materiál může být vytvořen v softwaru (a uložený v paměti), mohl by být vytvořen a uložený v modulu HARDWAROVÉho zabezpečení a tak dále.</span><span class="sxs-lookup"><span data-stu-id="a69b8-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a69b8-171">Rozhodujícím bodem je, že jakákoli dvě volání CreateNewDescriptor by nikdy neměla vytvářet ekvivalentní instance IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="a69b8-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a69b8-172">Typ AlgorithmConfiguration slouží jako vstupní bod pro rutiny vytváření klíčů, jako je například [automatické zavedení klíče](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a69b8-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a69b8-173">Chcete-li změnit implementaci pro všechny budoucí klíče, nastavte vlastnost AuthenticatedEncryptorConfiguration v KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="a69b8-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="a69b8-174">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="a69b8-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a69b8-175">Rozhraní **IAuthenticatedEncryptorConfiguration** představuje typ, který ví, jak vytvořit instance [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="a69b8-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a69b8-176">Zpřístupňuje jedno rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a69b8-176">It exposes a single API.</span></span>

* <span data-ttu-id="a69b8-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a69b8-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a69b8-178">IAuthenticatedEncryptorConfiguration se považujte za továrnu nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="a69b8-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="a69b8-179">Konfigurace slouží jako šablona.</span><span class="sxs-lookup"><span data-stu-id="a69b8-179">The configuration serves as a template.</span></span> <span data-ttu-id="a69b8-180">Zalomí informace o algoritmech (např. Tato konfigurace vytvoří popisovače pomocí GCM hlavního klíče AES-128), ale ještě není přidružená k určitému klíči.</span><span class="sxs-lookup"><span data-stu-id="a69b8-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a69b8-181">Když se zavolá CreateNewDescriptor, vytvoří se nový klíčový materiál výhradně pro toto volání a vytvoří se nový IAuthenticatedEncryptorDescriptor, který zabalí tento klíčový materiál a algoritmy, které jsou nutné k využívání materiálu.</span><span class="sxs-lookup"><span data-stu-id="a69b8-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a69b8-182">Klíčový materiál může být vytvořen v softwaru (a uložený v paměti), mohl by být vytvořen a uložený v modulu HARDWAROVÉho zabezpečení a tak dále.</span><span class="sxs-lookup"><span data-stu-id="a69b8-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a69b8-183">Rozhodujícím bodem je, že jakákoli dvě volání CreateNewDescriptor by nikdy neměla vytvářet ekvivalentní instance IAuthenticatedEncryptorDescriptor.</span><span class="sxs-lookup"><span data-stu-id="a69b8-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a69b8-184">Typ IAuthenticatedEncryptorConfiguration slouží jako vstupní bod pro rutiny vytváření klíčů, jako je například [automatické zavedení klíče](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a69b8-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a69b8-185">Pokud chcete změnit implementaci pro všechny budoucí klíče, zaregistrujte v kontejneru služby IAuthenticatedEncryptorConfiguration typu singleton.</span><span class="sxs-lookup"><span data-stu-id="a69b8-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
