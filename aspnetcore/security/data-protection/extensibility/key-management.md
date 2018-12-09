---
title: Rozšiřitelnost správy klíčů v ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core Data Protection rozšiřitelnost správy klíčů.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: e5ed2a65355a1dba34af09379f2583b3e73c24d7
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121424"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Rozšiřitelnost správy klíčů v ASP.NET Core

> [!TIP]
> Přečtěte si [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) části před čtením této části, jak vysvětluje některé základní principy tato rozhraní API.

> [!WARNING]
> Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.

## <a name="key"></a>Key

`IKey` Rozhraní je základní vyjádření key v cryptosystem. Výraz používá se tady v abstraktní smysl, ne v literálu smysl "kryptografické klíče". Klíč má následující vlastnosti:

* Aktivace, vytváření a datum vypršení platnosti

* Stav odvolání

* Identifikátor klíče (GUID)

::: moniker range=">= aspnetcore-2.0"

Kromě toho `IKey` zpřístupňuje `CreateEncryptor` metodu, která slouží k vytvoření [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance vázané na tento klíč.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Kromě toho `IKey` zpřístupňuje `CreateEncryptorInstance` metodu, která slouží k vytvoření [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance vázané na tento klíč.

::: moniker-end

> [!NOTE]
> Neexistuje žádné rozhraní API k načtení nezpracovaná kryptografický materiál ze `IKey` instance.

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager` Rozhraní představuje objekt zodpovídající za obecné úložiště klíčů, načítání a manipulaci s. Poskytuje tři základní operace:

* Vytvořte nový klíč a trvale uchovávat.

* Získáte všechny klíče ze služby storage.

* Odvolat jeden nebo více klíčů a zachovat informace o odvolání certifikátů do úložiště.

>[!WARNING]
> Zápis `IKeyManager` je velmi pokročilé úlohy a většinou vývojáři by se neměly pokoušet ho. Většina vývojářů by místo toho využít výhod zařízení, které nabízí [XmlKeyManager](#xmlkeymanager) třídy.

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager` Typ je v poli konkrétní implementace `IKeyManager`. Poskytuje několik užitečných zařízení, včetně klíčů v úschově a šifrovacích klíčů v klidovém stavu. Klíče v tomto systému jsou reprezentovány jako prvky jazyka XML (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` závisí na několika komponent tím plnící úkoly:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, který určuje algoritmy používané nové klíče.

* `IXmlRepository`, které ovládací prvky, kde jsou klíče uchovávaných v úložišti.

* `IXmlEncryptor` [volitelné], který umožňuje šifrování klíčů v klidovém stavu.

* `IKeyEscrowSink` [volitelné], který poskytuje služby klíčů v úschově.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, které ovládací prvky, kde jsou klíče uchovávaných v úložišti.

* `IXmlEncryptor` [volitelné], který umožňuje šifrování klíčů v klidovém stavu.

* `IKeyEscrowSink` [volitelné], který poskytuje služby klíčů v úschově.

::: moniker-end

Níže jsou uvedeny diagramy vysoké úrovně, které označují, jak jsou tyto součásti drátové společně v rámci `XmlKeyManager`.

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

*Vytvoření klíče / CreateNewKey*

Při provádění `CreateNewKey`, `AlgorithmConfiguration` komponenty se používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, který je pak serializovanou jako XML. Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení. Nešifrované XML je spusťte `IXmlEncryptor` (v případě potřeby) ke generování šifrovaných dokumentu XML. Tato zašifrovaného dokumentu se ukládají do dlouhodobého úložiště prostřednictvím `IXmlRepository`. (Pokud ne `IXmlEncryptor` je nakonfigurován, nešifrované dokumentu se ukládají v `IXmlRepository`.)

![Načítání klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

*Vytvoření klíče / CreateNewKey*

Při provádění `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` komponenty se používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, který je pak serializovanou jako XML. Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení. Nešifrované XML je spusťte `IXmlEncryptor` (v případě potřeby) ke generování šifrovaných dokumentu XML. Tato zašifrovaného dokumentu se ukládají do dlouhodobého úložiště prostřednictvím `IXmlRepository`. (Pokud ne `IXmlEncryptor` je nakonfigurován, nešifrované dokumentu se ukládají v `IXmlRepository`.)

![Načítání klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

*Načítání klíče / GetAllKeys*

Při provádění `GetAllKeys`, XML dokumenty představující klíče a zrušení se načítají z podkladové `IXmlRepository`. Pokud jsou tyto dokumenty zašifrované, systém bude automaticky jejich dešifrování. `XmlKeyManager` vytvoří odpovídající `IAuthenticatedEncryptorDescriptorDeserializer` instancí k deserializaci dokumenty zpět do `IAuthenticatedEncryptorDescriptor` instance, které jsou poté zabalené v jednotlivých `IKey` instancí. Tato kolekce `IKey` instancí je vrátit zpět volajícímu.

Další informace o konkrétní prvky XML najdete v [úložiště klíčů formátovat dokument](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository` Rozhraní představuje typ, který lze zachovat XML do a načtení XML ze záložního úložiště. Poskytuje dvě rozhraní API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementace `IXmlRepository` nemusíte analyzovat kód XML je procházející. Jejich považovat za neprůhledné dokumentů XML a umožní vyšší vrstvy starat o generování a analýzu dokumenty.

Existují čtyři integrované konkrétní typy, které implementují `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Najdete v článku [dokumentu poskytovatele úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers) Další informace.

Registrace vlastního `IXmlRepository` je vhodné při použití záložní úložiště (například Azure Table Storage).

Chcete-li změnit výchozí úložiště celou aplikaci zaregistrovat vlastní `IXmlRepository` instance:

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor` Rozhraní představuje typ, který můžete šifrovat element XML ve formátu prostého textu. Poskytuje jediné rozhraní API:

* Šifrování (XElement plaintextElement): EncryptedXmlInfo

Pokud serializovaného `IAuthenticatedEncryptorDescriptor` obsahuje všechny prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` spustí tyto prvky prostřednictvím nakonfigurované `IXmlEncryptor`společnosti `Encrypt` metoda a zachová enciphered element místo ve formátu prostého textu elementu `IXmlRepository`. Výstup `Encrypt` metoda je `EncryptedXmlInfo` objektu. Tento objekt je obálka, který obsahuje výsledné enciphered `XElement` a typ, který představuje `IXmlDecryptor` které lze používat k dešifrování odpovídající prvek.

Existují čtyři integrované konkrétní typy, které implementují `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Zobrazit [šifrování klíčů v dokumentu rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další informace.

Chcete-li změnit výchozí klíč šifrování v rest mechanismus celou aplikaci zaregistrovat vlastní `IXmlEncryptor` instance:

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor` Rozhraní představuje typ, který umí k dešifrování `XElement` , který byl enciphered prostřednictvím `IXmlEncryptor`. Poskytuje jediné rozhraní API:

* Dešifrování (XElement encryptedElement): XElement

`Decrypt` Metoda zruší šifrování provádí `IXmlEncryptor.Encrypt`. Obecně platí, každý konkrétní `IXmlEncryptor` implementace bude mít odpovídající konkrétní `IXmlDecryptor` implementace.

Typy, které implementují rozhraní `IXmlDecryptor` musí mít jednu z následujících dvou veřejných konstruktorů:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> `IServiceProvider` Předaný konstruktoru může mít hodnotu null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink` Rozhraní představuje typ, který může provádět v úschově citlivých informací. Připomínáme, že serializované popisovače může obsahovat citlivé údaje (například kryptografický materiál) a je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) zadejte na prvním místě. Však dojít nehodách a aktualizační kanály klíč lze odstranit nebo dojde k poškození.

Poskytuje rozhraní v úschově nouzový únikový poklop, umožňuje přístup k nezpracované serializovaném kódu XML předtím, než se transformuje žádné nakonfigurované [IXmlEncryptor](#ixmlencryptor). Rozhraní poskytuje jediné rozhraní API:

* Store (keyId identifikátor Guid, XElement element)

Záleží na `IKeyEscrowSink` implementace pro zpracování zadaný element bezpečným způsobem souladu s firemní zásady. Jednu možnou implementaci může být pro jímku podmíněné k zašifrování elementu XML pomocí známých podnikový certifikát X.509, kde má byl privátní klíč certifikátu mezi; `CertificateXmlEncryptor` typ s tím pomůže. `IKeyEscrowSink` Implementace je zodpovědné za trvalost zadaný element odpovídajícím způsobem.

Ve výchozím nastavení je povoleno žádný mechanismus, který v úschově, i když může správcům serverů [nastavit tuto konfiguraci globálně](xref:security/data-protection/configuration/machine-wide-policy). To lze také nakonfigurovat prostřednictvím kódu programu přes `IDataProtectionBuilder.AddKeyEscrowSink` způsob, jak je znázorněno v následující ukázce. `AddKeyEscrowSink` Zrcadlení přetížení metody `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, jako `IKeyEscrowSink` instancí mají být jednotlivé prvky. Pokud je položek víc `IKeyEscrowSink` jsou instance registrovány, každý z nich bude volána při generování klíčů, tak klíčů můžete mezi více mechanismů současně.

Neexistuje žádné rozhraní API ke čtení materiál ze `IKeyEscrowSink` instance. To je konzistentní s návrhu teorie mechanismus v úschově: má určený zpřístupňovaly materiál klíče důvěryhodné autority, a protože aplikace není samotné důvěryhodné autority, by neměl mít přístup k vlastním escrowed materiálu.

Následující ukázkový kód ukazuje vytvoření a registrace `IKeyEscrowSink` kde jsou klíče mezi tak, že je lze obnovit pouze členové "CONTOSODomain Admins".

> [!NOTE]
> Pokud chcete tuto ukázku spustit, musíte být v doméně systému Windows 8 / počítačů Windows serveru 2012 a řadič domény musí být Windows Server 2012 nebo novější.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
