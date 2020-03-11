---
title: Rozšiřitelnost správy klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core Data Protection rozšiřitelnost správy klíčů.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665877"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="e9cb5-103">Rozšiřitelnost správy klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9cb5-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="e9cb5-104">Před čtením této části si přečtěte část [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) , protože vysvětluje některé základní koncepce za těmito rozhraními API.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="e9cb5-105">Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="e9cb5-106">Klíč</span><span class="sxs-lookup"><span data-stu-id="e9cb5-106">Key</span></span>

<span data-ttu-id="e9cb5-107">Rozhraní `IKey` je základní reprezentace klíče v cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="e9cb5-108">Výraz používá se tady v abstraktní smysl, ne v literálu smysl "kryptografické klíče".</span><span class="sxs-lookup"><span data-stu-id="e9cb5-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="e9cb5-109">Klíč má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-109">A key has the following properties:</span></span>

* <span data-ttu-id="e9cb5-110">Aktivace, vytváření a datum vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="e9cb5-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="e9cb5-111">Stav odvolání</span><span class="sxs-lookup"><span data-stu-id="e9cb5-111">Revocation status</span></span>

* <span data-ttu-id="e9cb5-112">Identifikátor klíče (GUID)</span><span class="sxs-lookup"><span data-stu-id="e9cb5-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e9cb5-113">Kromě toho `IKey` zveřejňuje `CreateEncryptor` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e9cb5-114">Kromě toho `IKey` zveřejňuje `CreateEncryptorInstance` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e9cb5-115">Neexistuje žádné rozhraní API k načtení nezpracovaného kryptografického materiálu z instance `IKey`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="e9cb5-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="e9cb5-116">IKeyManager</span></span>

<span data-ttu-id="e9cb5-117">Rozhraní `IKeyManager` představuje objekt zodpovědný za obecné úložiště klíčů, načítání a manipulaci.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="e9cb5-118">Poskytuje tři základní operace:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="e9cb5-119">Vytvořte nový klíč a trvale uchovávat.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="e9cb5-120">Získáte všechny klíče ze služby storage.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-120">Get all keys from storage.</span></span>

* <span data-ttu-id="e9cb5-121">Odvolat jeden nebo více klíčů a zachovat informace o odvolání certifikátů do úložiště.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="e9cb5-122">Zápis `IKeyManager` je velmi pokročilý úkol a většina vývojářů se k tomu nepokouší.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="e9cb5-123">Místo toho by měla většina vývojářů využít výhod zařízení nabízených třídou [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="e9cb5-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="e9cb5-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="e9cb5-124">XmlKeyManager</span></span>

<span data-ttu-id="e9cb5-125">Typ `XmlKeyManager` je v rámci konkrétní implementace `IKeyManager`v krabicích.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="e9cb5-126">Poskytuje několik užitečných zařízení, včetně klíčů v úschově a šifrovacích klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="e9cb5-127">Klíče v tomto systému jsou reprezentovány jako XML elementy (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="e9cb5-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="e9cb5-128">`XmlKeyManager` závisí na několika dalších součástech v průběhu plnění úkolů:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e9cb5-129">`AlgorithmConfiguration`, která určuje algoritmy používané novými klíči.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="e9cb5-130">`IXmlRepository`, které ovládací prvky, ve kterých se ukládají klíče do úložiště.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e9cb5-131">`IXmlEncryptor` [volitelné], které povoluje šifrování klíčů v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e9cb5-132">`IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="e9cb5-133">`IXmlRepository`, které ovládací prvky, ve kterých se ukládají klíče do úložiště.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e9cb5-134">`IXmlEncryptor` [volitelné], které povoluje šifrování klíčů v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e9cb5-135">`IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="e9cb5-136">Níže jsou uvedeny diagramy vysoké úrovně, které určují, jak jsou tyto komponenty v rámci `XmlKeyManager`propojeny společně.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

<span data-ttu-id="e9cb5-138">*Vytvoření nebo CreateNewKey klíče*</span><span class="sxs-lookup"><span data-stu-id="e9cb5-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e9cb5-139">V implementaci `CreateNewKey`se `AlgorithmConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e9cb5-140">Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e9cb5-141">Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud je to potřeba) pro vygenerování šifrovaného dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e9cb5-142">Tento zašifrovaný dokument se trvale ukládá do dlouhodobého úložiště prostřednictvím `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e9cb5-143">(Pokud není nakonfigurovaná žádná `IXmlEncryptor`, nezašifrovaný dokument je uložený v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="e9cb5-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načítání klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

<span data-ttu-id="e9cb5-146">*Vytvoření nebo CreateNewKey klíče*</span><span class="sxs-lookup"><span data-stu-id="e9cb5-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e9cb5-147">V implementaci `CreateNewKey`se `IAuthenticatedEncryptorConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e9cb5-148">Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e9cb5-149">Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud je to potřeba) pro vygenerování šifrovaného dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e9cb5-150">Tento zašifrovaný dokument se trvale ukládá do dlouhodobého úložiště prostřednictvím `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e9cb5-151">(Pokud není nakonfigurovaná žádná `IXmlEncryptor`, nezašifrovaný dokument je uložený v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="e9cb5-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načítání klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="e9cb5-153">*Načtení klíče/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="e9cb5-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="e9cb5-154">V implementaci `GetAllKeys`jsou dokumenty XML reprezentující klíče a odvolání čteny ze základní `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="e9cb5-155">Pokud jsou tyto dokumenty zašifrované, systém bude automaticky jejich dešifrování.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="e9cb5-156">`XmlKeyManager` vytvoří příslušné instance `IAuthenticatedEncryptorDescriptorDeserializer` k deserializaci dokumentů zpět do instancí `IAuthenticatedEncryptorDescriptor`, které jsou následně zabaleny do jednotlivých instancí `IKey`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="e9cb5-157">Tato kolekce instancí `IKey` se vrátí volajícímu.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="e9cb5-158">Další informace o konkrétních prvcích XML najdete v [dokumentu formátu úložiště klíčů](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="e9cb5-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="e9cb5-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-159">IXmlRepository</span></span>

<span data-ttu-id="e9cb5-160">Rozhraní `IXmlRepository` představuje typ, který může uchovávat XML pro a načíst XML z záložního úložiště.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="e9cb5-161">Poskytuje dvě rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-161">It exposes two APIs:</span></span>

* <span data-ttu-id="e9cb5-162">`GetAllElements`:`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="e9cb5-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="e9cb5-163">Implementace `IXmlRepository` nepotřebují analyzovat XML předáváním.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="e9cb5-164">Jejich považovat za neprůhledné dokumentů XML a umožní vyšší vrstvy starat o generování a analýzu dokumenty.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="e9cb5-165">Existují čtyři předdefinované konkrétní typy, které implementují `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="e9cb5-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e9cb5-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e9cb5-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e9cb5-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="e9cb5-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e9cb5-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e9cb5-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e9cb5-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e9cb5-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="e9cb5-174">Další informace najdete v [dokumentu Zprostředkovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="e9cb5-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="e9cb5-175">Registrace vlastního `IXmlRepository` je vhodná při použití jiného záložního úložiště (například Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="e9cb5-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="e9cb5-176">Pokud chcete změnit výchozí aplikační úložiště v rámci aplikace, zaregistrujte vlastní instanci `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="e9cb5-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-177">IXmlEncryptor</span></span>

<span data-ttu-id="e9cb5-178">Rozhraní `IXmlEncryptor` představuje typ, který může šifrovat prostý text XML elementu.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="e9cb5-179">Poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-179">It exposes a single API:</span></span>

* <span data-ttu-id="e9cb5-180">Šifrování (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="e9cb5-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="e9cb5-181">Pokud serializovaná `IAuthenticatedEncryptorDescriptor` obsahuje nějaké prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` tyto prvky spustí prostřednictvím nakonfigurované `IXmlEncryptor`metody `Encrypt` a zachová element enciphered namísto elementu prostého textu do `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="e9cb5-182">Výstupem metody `Encrypt` je objekt `EncryptedXmlInfo`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="e9cb5-183">Tento objekt je obálka obsahující jak výsledný enciphered `XElement`, tak typ, který představuje `IXmlDecryptor`, které lze použít k dekódování odpovídajícího prvku.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="e9cb5-184">Existují čtyři předdefinované konkrétní typy, které implementují `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="e9cb5-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="e9cb5-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="e9cb5-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="e9cb5-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="e9cb5-189">Další informace najdete v [dokumentu REST v části šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="e9cb5-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="e9cb5-190">Chcete-li změnit výchozí klíč-šifrování na úrovni aplikace v klidovém stavu, zaregistrujte vlastní instanci `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="e9cb5-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="e9cb5-191">IXmlDecryptor</span></span>

<span data-ttu-id="e9cb5-192">Rozhraní `IXmlDecryptor` představuje typ, který ví, jak dešifrovat `XElement` enciphered prostřednictvím `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="e9cb5-193">Poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-193">It exposes a single API:</span></span>

* <span data-ttu-id="e9cb5-194">Dešifrování (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="e9cb5-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="e9cb5-195">Metoda `Decrypt` odvolá šifrování prováděné `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="e9cb5-196">Obecně platí, že každá konkrétní implementace `IXmlEncryptor` bude mít odpovídající konkrétní implementaci `IXmlDecryptor`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="e9cb5-197">Typy, které implementují `IXmlDecryptor` by měly mít jeden z následujících dvou veřejných konstruktorů:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="e9cb5-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="e9cb5-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="e9cb5-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="e9cb5-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="e9cb5-200">`IServiceProvider` předaný konstruktoru může mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="e9cb5-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="e9cb5-201">IKeyEscrowSink</span></span>

<span data-ttu-id="e9cb5-202">Rozhraní `IKeyEscrowSink` představuje typ, který může provádět v úschově citlivých informací.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="e9cb5-203">Odvolání těchto serializovaných popisovačů může obsahovat citlivé údaje (například kryptografický materiál) a to je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) typu na první místo.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="e9cb5-204">Však dojít nehodách a aktualizační kanály klíč lze odstranit nebo dojde k poškození.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="e9cb5-205">Rozhraní v úschově poskytuje nouzový řídicí šrafování, který umožňuje přístup k nezpracovanému serializovanému XML před jeho transformací všemi nakonfigurovanými [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="e9cb5-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="e9cb5-206">Rozhraní poskytuje jediné rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e9cb5-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="e9cb5-207">Store (keyId identifikátor Guid, XElement element)</span><span class="sxs-lookup"><span data-stu-id="e9cb5-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="e9cb5-208">Je to až `IKeyEscrowSink` implementace, aby byl poskytnutý prvek bezpečným způsobem konzistentním s obchodními zásadami.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="e9cb5-209">Jednou z možných implementací může být, aby jímka v úschově zašifroval XML element pomocí známého podnikového certifikátu X. 509, ve kterém byl privátní klíč certifikátu uloží; typ `CertificateXmlEncryptor` může pomoct s tímto.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="e9cb5-210">`IKeyEscrowSink` implementace je také zodpovědná za trvalé uchování poskytnutého prvku.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="e9cb5-211">Ve výchozím nastavení není povolený žádný mechanismus v úschově, i když správci serveru můžou [tuto globálně nakonfigurovat](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="e9cb5-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="e9cb5-212">Dá se taky nakonfigurovat programově prostřednictvím metody `IDataProtectionBuilder.AddKeyEscrowSink`, jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="e9cb5-213">Metoda `AddKeyEscrowSink` přetížení zrcadlí `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, protože instance `IKeyEscrowSink` mají být typu singleton.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="e9cb5-214">Pokud je zaregistrováno více instancí `IKeyEscrowSink`, každá z nich bude volána během generování klíče, takže klíče mohou být uloží k několika mechanismům současně.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="e9cb5-215">Neexistuje žádné rozhraní API pro čtení materiálu z instance `IKeyEscrowSink`.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="e9cb5-216">To je konzistentní s návrhu teorie mechanismus v úschově: má určený zpřístupňovaly materiál klíče důvěryhodné autority, a protože aplikace není samotné důvěryhodné autority, by neměl mít přístup k vlastním escrowed materiálu.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="e9cb5-217">Následující vzorový kód ukazuje vytvoření a registraci `IKeyEscrowSink`, kde jsou klíče uloží, aby je mohli obnovit pouze členové "CONTOSODomain Admins".</span><span class="sxs-lookup"><span data-stu-id="e9cb5-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="e9cb5-218">Pokud chcete tuto ukázku spustit, musíte být v doméně systému Windows 8 / počítačů Windows serveru 2012 a řadič domény musí být Windows Server 2012 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="e9cb5-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
