---
title: Rozšiřitelnost správy klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core rozšiřitelnosti správy klíčů ochrany dat.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: f8af699344473510c5579c2f0e4d2920ada013f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775723"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="929ec-103">Rozšiřitelnost správy klíčů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="929ec-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="929ec-104">Před čtením této části si přečtěte část [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) , protože vysvětluje některé základní koncepce za těmito rozhraními API.</span><span class="sxs-lookup"><span data-stu-id="929ec-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="929ec-105">Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.</span><span class="sxs-lookup"><span data-stu-id="929ec-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="929ec-106">Key</span><span class="sxs-lookup"><span data-stu-id="929ec-106">Key</span></span>

<span data-ttu-id="929ec-107">`IKey` Rozhraní je základní reprezentace klíče v cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="929ec-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="929ec-108">Pojem Key se tady používá v abstraktním smyslu, nikoli v literálu "materiál kryptografického klíče".</span><span class="sxs-lookup"><span data-stu-id="929ec-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="929ec-109">Klíč má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="929ec-109">A key has the following properties:</span></span>

* <span data-ttu-id="929ec-110">Data o aktivaci, vytváření a vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="929ec-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="929ec-111">Stav odvolání</span><span class="sxs-lookup"><span data-stu-id="929ec-111">Revocation status</span></span>

* <span data-ttu-id="929ec-112">Identifikátor klíče (identifikátor GUID)</span><span class="sxs-lookup"><span data-stu-id="929ec-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="929ec-113">Navíc zpřístupňuje `IKey` `CreateEncryptor` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.</span><span class="sxs-lookup"><span data-stu-id="929ec-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="929ec-114">Navíc zpřístupňuje `IKey` `CreateEncryptorInstance` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.</span><span class="sxs-lookup"><span data-stu-id="929ec-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="929ec-115">Neexistuje žádné rozhraní API pro načtení nezpracovaného kryptografického materiálu `IKey` z instance.</span><span class="sxs-lookup"><span data-stu-id="929ec-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="929ec-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="929ec-116">IKeyManager</span></span>

<span data-ttu-id="929ec-117">`IKeyManager` Rozhraní představuje objekt zodpovědný za obecné úložiště klíčů, načítání a manipulaci.</span><span class="sxs-lookup"><span data-stu-id="929ec-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="929ec-118">Zveřejňuje tři operace vysoké úrovně:</span><span class="sxs-lookup"><span data-stu-id="929ec-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="929ec-119">Vytvořte nový klíč a zachovejte ho do úložiště.</span><span class="sxs-lookup"><span data-stu-id="929ec-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="929ec-120">Získá všechny klíče z úložiště.</span><span class="sxs-lookup"><span data-stu-id="929ec-120">Get all keys from storage.</span></span>

* <span data-ttu-id="929ec-121">Odvolejte jeden nebo více klíčů a zachovejte informace o odvolání úložiště.</span><span class="sxs-lookup"><span data-stu-id="929ec-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="929ec-122">Psaní `IKeyManager` je velmi pokročilý úkol a většina vývojářů ji nezkouší.</span><span class="sxs-lookup"><span data-stu-id="929ec-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="929ec-123">Místo toho by měla většina vývojářů využít výhod zařízení nabízených třídou [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="929ec-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="929ec-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="929ec-124">XmlKeyManager</span></span>

<span data-ttu-id="929ec-125">`XmlKeyManager` Typ je konkrétní implementace `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="929ec-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="929ec-126">Nabízí několik užitečných zařízení, včetně služby Key v úschově a šifrování neaktivních klíčů.</span><span class="sxs-lookup"><span data-stu-id="929ec-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="929ec-127">Klíče v tomto systému jsou reprezentovány jako XML elementy (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="929ec-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="929ec-128">`XmlKeyManager`závisí na několika dalších součástech v průběhu plnění úkolů:</span><span class="sxs-lookup"><span data-stu-id="929ec-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="929ec-129">`AlgorithmConfiguration`, který určuje algoritmy používané novými klíči.</span><span class="sxs-lookup"><span data-stu-id="929ec-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="929ec-130">`IXmlRepository`, které řídí, kde jsou klíče uchovávány v úložišti.</span><span class="sxs-lookup"><span data-stu-id="929ec-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="929ec-131">`IXmlEncryptor`[volitelné], což umožňuje šifrování klíčů v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="929ec-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="929ec-132">`IKeyEscrowSink`[volitelné], které poskytuje službu Key v úschově Services.</span><span class="sxs-lookup"><span data-stu-id="929ec-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="929ec-133">`IXmlRepository`, které řídí, kde jsou klíče uchovávány v úložišti.</span><span class="sxs-lookup"><span data-stu-id="929ec-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="929ec-134">`IXmlEncryptor`[volitelné], což umožňuje šifrování klíčů v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="929ec-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="929ec-135">`IKeyEscrowSink`[volitelné], které poskytuje službu Key v úschově Services.</span><span class="sxs-lookup"><span data-stu-id="929ec-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="929ec-136">Níže jsou uvedeny diagramy vysoké úrovně, které určují, jak jsou tyto komponenty v `XmlKeyManager`rámci společné.</span><span class="sxs-lookup"><span data-stu-id="929ec-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

<span data-ttu-id="929ec-138">*Vytvoření nebo CreateNewKey klíče*</span><span class="sxs-lookup"><span data-stu-id="929ec-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="929ec-139">V implementaci `CreateNewKey`nástroje se `AlgorithmConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML.</span><span class="sxs-lookup"><span data-stu-id="929ec-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="929ec-140">Pokud je přítomná jímka služby Key v úschově, nezpracovaný (nešifrovaný) kód XML je poskytnut jímka pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="929ec-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="929ec-141">Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud se vyžaduje) pro vygenerování šifrovaného dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="929ec-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="929ec-142">Tento zašifrovaný dokument je trvalý v `IXmlRepository`dlouhodobém úložišti prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="929ec-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="929ec-143">(Pokud `IXmlEncryptor` není nakonfigurováno, nešifrovaný dokument je uložen v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="929ec-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načtení klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

<span data-ttu-id="929ec-146">*Vytvoření nebo CreateNewKey klíče*</span><span class="sxs-lookup"><span data-stu-id="929ec-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="929ec-147">V implementaci `CreateNewKey`nástroje se `IAuthenticatedEncryptorConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML.</span><span class="sxs-lookup"><span data-stu-id="929ec-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="929ec-148">Pokud je přítomná jímka služby Key v úschově, nezpracovaný (nešifrovaný) kód XML je poskytnut jímka pro dlouhodobé uložení.</span><span class="sxs-lookup"><span data-stu-id="929ec-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="929ec-149">Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud se vyžaduje) pro vygenerování šifrovaného dokumentu XML.</span><span class="sxs-lookup"><span data-stu-id="929ec-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="929ec-150">Tento zašifrovaný dokument je trvalý v `IXmlRepository`dlouhodobém úložišti prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="929ec-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="929ec-151">(Pokud `IXmlEncryptor` není nakonfigurováno, nešifrovaný dokument je uložen v `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="929ec-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Načtení klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="929ec-153">*Načtení klíče/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="929ec-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="929ec-154">V implementaci `GetAllKeys`nástroje jsou dokumenty XML reprezentující klíče a odvolání čteny ze základní `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="929ec-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="929ec-155">Pokud jsou tyto dokumenty šifrovány, systém je bude automaticky dešifroval.</span><span class="sxs-lookup"><span data-stu-id="929ec-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="929ec-156">`XmlKeyManager`Vytvoří vhodné `IAuthenticatedEncryptorDescriptorDeserializer` instance pro rekonstrukci dokumentů zpět do `IAuthenticatedEncryptorDescriptor` instancí, které jsou poté zabaleny do jednotlivých `IKey` instancí.</span><span class="sxs-lookup"><span data-stu-id="929ec-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="929ec-157">Tato kolekce `IKey` instancí se vrátí volajícímu.</span><span class="sxs-lookup"><span data-stu-id="929ec-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="929ec-158">Další informace o konkrétních prvcích XML najdete v [dokumentu formátu úložiště klíčů](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="929ec-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="929ec-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-159">IXmlRepository</span></span>

<span data-ttu-id="929ec-160">`IXmlRepository` Rozhraní představuje typ, který může uchovávat XML a načíst XML z záložního úložiště.</span><span class="sxs-lookup"><span data-stu-id="929ec-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="929ec-161">Zpřístupňuje dvě rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="929ec-161">It exposes two APIs:</span></span>

* <span data-ttu-id="929ec-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="929ec-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="929ec-163">`IXmlRepository` Implementace nepotřebují analyzovat kód XML, který je projde.</span><span class="sxs-lookup"><span data-stu-id="929ec-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="929ec-164">Měly by se dokumenty XML nacházet jako neprůhledné a umožňují rychlejší vytváření a analýzu dokumentů.</span><span class="sxs-lookup"><span data-stu-id="929ec-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="929ec-165">Existují čtyři předdefinované konkrétní typy, které implementují `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="929ec-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="929ec-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="929ec-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="929ec-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="929ec-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="929ec-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="929ec-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="929ec-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="929ec-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="929ec-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="929ec-174">Další informace najdete v [dokumentu Zprostředkovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="929ec-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="929ec-175">Vlastní `IXmlRepository` registrace je vhodná při použití jiného záložního úložiště (například Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="929ec-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="929ec-176">Pokud chcete změnit výchozí aplikační úložiště v rámci aplikace, zaregistrujte si vlastní `IXmlRepository` instanci:</span><span class="sxs-lookup"><span data-stu-id="929ec-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="929ec-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-177">IXmlEncryptor</span></span>

<span data-ttu-id="929ec-178">`IXmlEncryptor` Rozhraní představuje typ, který může šifrovat prostý text XML elementu.</span><span class="sxs-lookup"><span data-stu-id="929ec-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="929ec-179">Zpřístupňuje jedno rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="929ec-179">It exposes a single API:</span></span>

* <span data-ttu-id="929ec-180">Šifrovat (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="929ec-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="929ec-181">Pokud serializovaná `IAuthenticatedEncryptorDescriptor` obsahuje nějaké prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` tyto prvky spustí prostřednictvím nakonfigurované `IXmlEncryptor` `Encrypt` metody a zachová element enciphered, nikoli elementu prostého textu do. `IXmlRepository`</span><span class="sxs-lookup"><span data-stu-id="929ec-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="929ec-182">Výstupem `Encrypt` metody je `EncryptedXmlInfo` objekt.</span><span class="sxs-lookup"><span data-stu-id="929ec-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="929ec-183">Tento objekt je obálkou, která obsahuje výsledný enciphered `XElement` a typ, který představuje, `IXmlDecryptor` který lze použít k dekódování odpovídajícího prvku.</span><span class="sxs-lookup"><span data-stu-id="929ec-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="929ec-184">Existují čtyři předdefinované konkrétní typy, které implementují `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="929ec-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="929ec-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="929ec-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="929ec-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="929ec-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="929ec-189">Další informace najdete v [dokumentu REST v části šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="929ec-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="929ec-190">Pro změnu výchozího mechanismu šifrování klíče v rámci aplikace na úrovni Standard, zaregistrujte vlastní `IXmlEncryptor` instanci:</span><span class="sxs-lookup"><span data-stu-id="929ec-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="929ec-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="929ec-191">IXmlDecryptor</span></span>

<span data-ttu-id="929ec-192">`IXmlDecryptor` Rozhraní představuje typ, který zná způsob dešifrování `XElement` , které bylo enciphered prostřednictvím `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="929ec-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="929ec-193">Zpřístupňuje jedno rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="929ec-193">It exposes a single API:</span></span>

* <span data-ttu-id="929ec-194">Dešifrovat (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="929ec-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="929ec-195">`Decrypt` Metoda odvolá šifrování prováděné `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="929ec-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="929ec-196">Obecně platí, že `IXmlEncryptor` každá konkrétní implementace bude mít odpovídající `IXmlDecryptor` konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="929ec-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="929ec-197">Typy, které `IXmlDecryptor` implementují, by měly mít jeden z následujících dvou veřejných konstruktorů:</span><span class="sxs-lookup"><span data-stu-id="929ec-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="929ec-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="929ec-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="929ec-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="929ec-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="929ec-200">`IServiceProvider` Předaný konstruktor může mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="929ec-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="929ec-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="929ec-201">IKeyEscrowSink</span></span>

<span data-ttu-id="929ec-202">`IKeyEscrowSink` Rozhraní představuje typ, který může provádět v úschově citlivých informací.</span><span class="sxs-lookup"><span data-stu-id="929ec-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="929ec-203">Odvolání těchto serializovaných popisovačů může obsahovat citlivé údaje (například kryptografický materiál) a to je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) typu na první místo.</span><span class="sxs-lookup"><span data-stu-id="929ec-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="929ec-204">Nicméně dojde k nehodám a zazvonění klíčů lze odstranit nebo poškodit.</span><span class="sxs-lookup"><span data-stu-id="929ec-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="929ec-205">Rozhraní v úschově poskytuje nouzový řídicí šrafování, který umožňuje přístup k nezpracovanému serializovanému XML před jeho transformací všemi nakonfigurovanými [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="929ec-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="929ec-206">Rozhraní zpřístupňuje jedno rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="929ec-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="929ec-207">Store (GUID keyId, XElement element)</span><span class="sxs-lookup"><span data-stu-id="929ec-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="929ec-208">Je to až do `IKeyEscrowSink` implementace, aby byl poskytnutý prvek bezpečným způsobem konzistentním s obchodními zásadami.</span><span class="sxs-lookup"><span data-stu-id="929ec-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="929ec-209">Jednou z možných implementací může být, aby jímka v úschově zašifroval XML element pomocí známého podnikového certifikátu X. 509, ve kterém byl privátní klíč certifikátu uloží; Tento `CertificateXmlEncryptor` typ může pomoct s tímto.</span><span class="sxs-lookup"><span data-stu-id="929ec-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="929ec-210">`IKeyEscrowSink` Implementace je také zodpovědná za trvalé uchování poskytnutého prvku.</span><span class="sxs-lookup"><span data-stu-id="929ec-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="929ec-211">Ve výchozím nastavení není povolený žádný mechanismus v úschově, i když správci serveru můžou [tuto globálně nakonfigurovat](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="929ec-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="929ec-212">Lze ji také programově nakonfigurovat prostřednictvím `IDataProtectionBuilder.AddKeyEscrowSink` metody, jak je znázorněno v následující ukázce.</span><span class="sxs-lookup"><span data-stu-id="929ec-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="929ec-213">`AddKeyEscrowSink` Metoda přetěžuje zrcadlení `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, protože `IKeyEscrowSink` instance mají být typu singleton.</span><span class="sxs-lookup"><span data-stu-id="929ec-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="929ec-214">Pokud je `IKeyEscrowSink` registrováno více instancí, každá z nich bude volána během generování klíče, takže klíče mohou být uloží k několika mechanismům současně.</span><span class="sxs-lookup"><span data-stu-id="929ec-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="929ec-215">Neexistuje žádné rozhraní API pro čtení materiálu z `IKeyEscrowSink` instance.</span><span class="sxs-lookup"><span data-stu-id="929ec-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="929ec-216">To je v souladu s tabulkou pro návrh v úschově mechanismu: je určena k tomu, aby byl materiál klíče přístupný pro důvěryhodnou autoritu a protože aplikace sám o sobě není důvěryhodnou autoritou, neměla by mít přístup ke svému vlastnímu uloží materiálu.</span><span class="sxs-lookup"><span data-stu-id="929ec-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="929ec-217">Následující vzorový kód ukazuje vytvoření a registraci klíčů `IKeyEscrowSink` , kde jsou uloží, aby je mohli obnovit pouze členové "CONTOSODomain Admins".</span><span class="sxs-lookup"><span data-stu-id="929ec-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="929ec-218">Pokud chcete tuto ukázku spustit, musíte být na počítači se systémem Windows 8/Windows Server 2012 připojeným k doméně a řadičem domény musí být Windows Server 2012 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="929ec-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
