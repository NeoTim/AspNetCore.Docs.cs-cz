---
title: Rozšiřitelnost správy klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core Data Protection rozšiřitelnost správy klíčů.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284601"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="b5b88-103">Rozšiřitelnost správy klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5b88-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="b5b88-104">Přečtěte si [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) části před čtením této části, jak vysvětluje některé základní principy tato rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b5b88-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="b5b88-105">Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.</span><span class="sxs-lookup"><span data-stu-id="b5b88-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="b5b88-106">Key</span><span class="sxs-lookup"><span data-stu-id="b5b88-106">Key</span></span>

<span data-ttu-id="b5b88-107">`IKey` Rozhraní je základní vyjádření key v cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="b5b88-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="b5b88-108">Výraz používá se tady v abstraktní smysl, ne v literálu smysl "kryptografické klíče".</span><span class="sxs-lookup"><span data-stu-id="b5b88-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="b5b88-109">Klíč má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b5b88-109">A key has the following properties:</span></span>

* <span data-ttu-id="b5b88-110">Aktivace, vytváření a datum vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="b5b88-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="b5b88-111">Stav odvolání</span><span class="sxs-lookup"><span data-stu-id="b5b88-111">Revocation status</span></span>

* <span data-ttu-id="b5b88-112">Identifikátor klíče (GUID)</span><span class="sxs-lookup"><span data-stu-id="b5b88-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b5b88-113">Kromě toho `IKey` zpřístupňuje `CreateEncryptor` metodu, která slouží k vytvoření [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance vázané na tento klíč.</span><span class="sxs-lookup"><span data-stu-id="b5b88-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b5b88-114">Kromě toho `IKey` zpřístupňuje `CreateEncryptorInstance` metodu, která slouží k vytvoření [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance vázané na tento klíč.</span><span class="sxs-lookup"><span data-stu-id="b5b88-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b5b88-115">Neexistuje žádné rozhraní API k načtení nezpracovaná kryptografický materiál ze `IKey` instance.</span><span class="sxs-lookup"><span data-stu-id="b5b88-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="b5b88-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="b5b88-116">IKeyManager</span></span>

<span data-ttu-id="b5b88-117">`IKeyManager` Rozhraní představuje objekt zodpovídající za obecné úložiště klíčů, načítání a manipulaci s.</span><span class="sxs-lookup"><span data-stu-id="b5b88-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="b5b88-118">Poskytuje tři základní operace:</span><span class="sxs-lookup"><span data-stu-id="b5b88-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="b5b88-119">Vytvořte nový klíč a trvale uchovávat.</span><span class="sxs-lookup"><span data-stu-id="b5b88-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="b5b88-120">Získáte všechny klíče ze služby storage.</span><span class="sxs-lookup"><span data-stu-id="b5b88-120">Get all keys from storage.</span></span>

* <span data-ttu-id="b5b88-121">Odvolat jeden nebo více klíčů a zachovat informace o odvolání certifikátů do úložiště.</span><span class="sxs-lookup"><span data-stu-id="b5b88-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="b5b88-122">Zápis `IKeyManager` je velmi pokročilé úlohy a většinou vývojáři by se neměly pokoušet ho.</span><span class="sxs-lookup"><span data-stu-id="b5b88-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="b5b88-123">Většina vývojářů by místo toho využít výhod zařízení, které nabízí [XmlKeyManager](#xmlkeymanager) třídy.</span><span class="sxs-lookup"><span data-stu-id="b5b88-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="b5b88-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="b5b88-124">XmlKeyManager</span></span>

<span data-ttu-id="b5b88-125">`XmlKeyManager` Typ je v poli konkrétní implementace `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="b5b88-126">Poskytuje několik užitečných zařízení, včetně klíčů v úschově a šifrovacích klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="b5b88-127">Klíče v tomto systému jsou reprezentovány jako prvky jazyka XML (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="b5b88-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="b5b88-128">`XmlKeyManager` závisí na několika komponent tím plnící úkoly:</span><span class="sxs-lookup"><span data-stu-id="b5b88-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="b5b88-129">`AlgorithmConfiguration`, který určuje algoritmy používané nové klíče.</span><span class="sxs-lookup"><span data-stu-id="b5b88-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="b5b88-130">`IXmlRepository`, které ovládací prvky, kde jsou klíče uchovávaných v úložišti.</span><span class="sxs-lookup"><span data-stu-id="b5b88-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="b5b88-131">`IXmlEncryptor` [volitelné], který umožňuje šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="b5b88-132">`IKeyEscrowSink` [volitelné], který poskytuje služby klíčů v úschově.</span><span class="sxs-lookup"><span data-stu-id="b5b88-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="b5b88-133">`IXmlRepository`, které ovládací prvky, kde jsou klíče uchovávaných v úložišti.</span><span class="sxs-lookup"><span data-stu-id="b5b88-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="b5b88-134">`IXmlEncryptor` [volitelné], který umožňuje šifrování klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="b5b88-135">`IKeyEscrowSink` [volitelné], který poskytuje služby klíčů v úschově.</span><span class="sxs-lookup"><span data-stu-id="b5b88-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="b5b88-136">Níže jsou uvedeny diagramy vysoké úrovně, které označují, jak jsou tyto součásti drátové společně v rámci `XmlKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

<span data-ttu-id="b5b88-138">*Vytvoření klíče / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="b5b88-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="b5b88-139">Při provádění `CreateNewKey`, `AlgorithmConfiguration` komponenty se používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, který je pak serializovanou jako XML.</span><span class="sxs-lookup"><span data-stu-id="b5b88-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="b5b88-140">Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="b5b88-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="b5b88-141">Nešifrované XML je spusťte `IXmlEncryptor` (v případě potřeby) ke generování šifrovaných dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="b5b88-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="b5b88-142">Tato zašifrovaného dokumentu se ukládají do dlouhodobého úložiště prostřednictvím `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="b5b88-143">(Pokud ne `IXmlEncryptor` je nakonfigurován, nešifrované dokumentu se ukládají v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="b5b88-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načítání klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

<span data-ttu-id="b5b88-146">*Vytvoření klíče / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="b5b88-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="b5b88-147">Při provádění `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` komponenty se používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, který je pak serializovanou jako XML.</span><span class="sxs-lookup"><span data-stu-id="b5b88-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="b5b88-148">Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="b5b88-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="b5b88-149">Nešifrované XML je spusťte `IXmlEncryptor` (v případě potřeby) ke generování šifrovaných dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="b5b88-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="b5b88-150">Tato zašifrovaného dokumentu se ukládají do dlouhodobého úložiště prostřednictvím `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="b5b88-151">(Pokud ne `IXmlEncryptor` je nakonfigurován, nešifrované dokumentu se ukládají v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="b5b88-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načítání klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="b5b88-153">*Načítání klíče / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="b5b88-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="b5b88-154">Při provádění `GetAllKeys`, XML dokumenty představující klíče a zrušení se načítají z podkladové `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="b5b88-155">Pokud jsou tyto dokumenty zašifrované, systém bude automaticky jejich dešifrování.</span><span class="sxs-lookup"><span data-stu-id="b5b88-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="b5b88-156">`XmlKeyManager` vytvoří odpovídající `IAuthenticatedEncryptorDescriptorDeserializer` instancí k deserializaci dokumenty zpět do `IAuthenticatedEncryptorDescriptor` instance, které jsou poté zabalené v jednotlivých `IKey` instancí.</span><span class="sxs-lookup"><span data-stu-id="b5b88-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="b5b88-157">Tato kolekce `IKey` instancí je vrátit zpět volajícímu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="b5b88-158">Další informace o konkrétní prvky XML najdete v [úložiště klíčů formátovat dokument](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="b5b88-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="b5b88-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-159">IXmlRepository</span></span>

<span data-ttu-id="b5b88-160">`IXmlRepository` Rozhraní představuje typ, který lze zachovat XML do a načtení XML ze záložního úložiště.</span><span class="sxs-lookup"><span data-stu-id="b5b88-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="b5b88-161">Poskytuje dvě rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="b5b88-161">It exposes two APIs:</span></span>

* <span data-ttu-id="b5b88-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="b5b88-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="b5b88-163">Implementace `IXmlRepository` nemusíte analyzovat kód XML je procházející.</span><span class="sxs-lookup"><span data-stu-id="b5b88-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="b5b88-164">Jejich považovat za neprůhledné dokumentů XML a umožní vyšší vrstvy starat o generování a analýzu dokumenty.</span><span class="sxs-lookup"><span data-stu-id="b5b88-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="b5b88-165">Existují čtyři integrované konkrétní typy, které implementují `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="b5b88-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="b5b88-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="b5b88-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="b5b88-168">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="b5b88-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="b5b88-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="b5b88-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="b5b88-172">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="b5b88-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="b5b88-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="b5b88-174">Najdete v článku [dokumentu poskytovatele úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers) Další informace.</span><span class="sxs-lookup"><span data-stu-id="b5b88-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="b5b88-175">Registrace vlastního `IXmlRepository` je vhodné při použití záložní úložiště (například Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="b5b88-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="b5b88-176">Chcete-li změnit výchozí úložiště celou aplikaci zaregistrovat vlastní `IXmlRepository` instance:</span><span class="sxs-lookup"><span data-stu-id="b5b88-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="b5b88-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-177">IXmlEncryptor</span></span>

<span data-ttu-id="b5b88-178">`IXmlEncryptor` Rozhraní představuje typ, který můžete šifrovat element XML ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="b5b88-179">Poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="b5b88-179">It exposes a single API:</span></span>

* <span data-ttu-id="b5b88-180">Encrypt(XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="b5b88-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="b5b88-181">Pokud serializovaného `IAuthenticatedEncryptorDescriptor` obsahuje všechny prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` spustí tyto prvky prostřednictvím nakonfigurované `IXmlEncryptor`společnosti `Encrypt` metoda a zachová enciphered element místo ve formátu prostého textu elementu `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="b5b88-182">Výstup `Encrypt` metoda je `EncryptedXmlInfo` objektu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="b5b88-183">Tento objekt je obálka, který obsahuje výsledné enciphered `XElement` a typ, který představuje `IXmlDecryptor` které lze používat k dešifrování odpovídající prvek.</span><span class="sxs-lookup"><span data-stu-id="b5b88-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="b5b88-184">Existují čtyři integrované konkrétní typy, které implementují `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="b5b88-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="b5b88-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="b5b88-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="b5b88-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="b5b88-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="b5b88-189">Zobrazit [šifrování klíčů v dokumentu rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další informace.</span><span class="sxs-lookup"><span data-stu-id="b5b88-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="b5b88-190">Chcete-li změnit výchozí klíč šifrování v rest mechanismus celou aplikaci zaregistrovat vlastní `IXmlEncryptor` instance:</span><span class="sxs-lookup"><span data-stu-id="b5b88-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="b5b88-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="b5b88-191">IXmlDecryptor</span></span>

<span data-ttu-id="b5b88-192">`IXmlDecryptor` Rozhraní představuje typ, který umí k dešifrování `XElement` , který byl enciphered prostřednictvím `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="b5b88-193">Poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="b5b88-193">It exposes a single API:</span></span>

* <span data-ttu-id="b5b88-194">Dešifrování (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="b5b88-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="b5b88-195">`Decrypt` Metoda zruší šifrování provádí `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="b5b88-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="b5b88-196">Obecně platí, každý konkrétní `IXmlEncryptor` implementace bude mít odpovídající konkrétní `IXmlDecryptor` implementace.</span><span class="sxs-lookup"><span data-stu-id="b5b88-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="b5b88-197">Typy, které implementují rozhraní `IXmlDecryptor` musí mít jednu z následujících dvou veřejných konstruktorů:</span><span class="sxs-lookup"><span data-stu-id="b5b88-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="b5b88-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="b5b88-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="b5b88-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="b5b88-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="b5b88-200">`IServiceProvider` Předaný konstruktoru může mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b5b88-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="b5b88-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="b5b88-201">IKeyEscrowSink</span></span>

<span data-ttu-id="b5b88-202">`IKeyEscrowSink` Rozhraní představuje typ, který může provádět v úschově citlivých informací.</span><span class="sxs-lookup"><span data-stu-id="b5b88-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="b5b88-203">Připomínáme, že serializované popisovače může obsahovat citlivé údaje (například kryptografický materiál) a je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) zadejte na prvním místě.</span><span class="sxs-lookup"><span data-stu-id="b5b88-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="b5b88-204">Však dojít nehodách a aktualizační kanály klíč lze odstranit nebo dojde k poškození.</span><span class="sxs-lookup"><span data-stu-id="b5b88-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="b5b88-205">Poskytuje rozhraní v úschově nouzový únikový poklop, umožňuje přístup k nezpracované serializovaném kódu XML předtím, než se transformuje žádné nakonfigurované [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="b5b88-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="b5b88-206">Rozhraní poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="b5b88-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="b5b88-207">Store (keyId identifikátor Guid, XElement element)</span><span class="sxs-lookup"><span data-stu-id="b5b88-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="b5b88-208">Záleží na `IKeyEscrowSink` implementace pro zpracování zadaný element bezpečným způsobem souladu s firemní zásady.</span><span class="sxs-lookup"><span data-stu-id="b5b88-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="b5b88-209">Jednu možnou implementaci může být pro jímku podmíněné k zašifrování elementu XML pomocí známých podnikový certifikát X.509, kde má byl privátní klíč certifikátu mezi; `CertificateXmlEncryptor` typ s tím pomůže.</span><span class="sxs-lookup"><span data-stu-id="b5b88-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="b5b88-210">`IKeyEscrowSink` Implementace je zodpovědné za trvalost zadaný element odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="b5b88-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="b5b88-211">Ve výchozím nastavení je povoleno žádný mechanismus, který v úschově, i když může správcům serverů [nastavit tuto konfiguraci globálně](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="b5b88-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="b5b88-212">To lze také nakonfigurovat prostřednictvím kódu programu přes `IDataProtectionBuilder.AddKeyEscrowSink` způsob, jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="b5b88-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="b5b88-213">`AddKeyEscrowSink` Zrcadlení přetížení metody `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, jako `IKeyEscrowSink` instancí mají být jednotlivé prvky.</span><span class="sxs-lookup"><span data-stu-id="b5b88-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="b5b88-214">Pokud je položek víc `IKeyEscrowSink` jsou instance registrovány, každý z nich bude volána při generování klíčů, tak klíčů můžete mezi více mechanismů současně.</span><span class="sxs-lookup"><span data-stu-id="b5b88-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="b5b88-215">Neexistuje žádné rozhraní API ke čtení materiál ze `IKeyEscrowSink` instance.</span><span class="sxs-lookup"><span data-stu-id="b5b88-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="b5b88-216">To je konzistentní s návrhu teorie mechanismus v úschově: má určený zpřístupňovaly materiál klíče důvěryhodné autority, a protože aplikace není samotné důvěryhodné autority, by neměl mít přístup k vlastním escrowed materiálu.</span><span class="sxs-lookup"><span data-stu-id="b5b88-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="b5b88-217">Následující ukázkový kód ukazuje vytvoření a registrace `IKeyEscrowSink` kde jsou klíče mezi tak, že je lze obnovit pouze členové "CONTOSODomain Admins".</span><span class="sxs-lookup"><span data-stu-id="b5b88-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b88-218">Pokud chcete tuto ukázku spustit, musíte být v doméně systému Windows 8 / počítačů Windows serveru 2012 a řadič domény musí být Windows Server 2012 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b5b88-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
