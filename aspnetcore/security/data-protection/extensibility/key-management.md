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
# <a name="key-management-extensibility-in-aspnet-core"></a>Rozšiřitelnost správy klíčů v ASP.NET Core

> [!TIP]
> Před čtením této části si přečtěte část [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) , protože vysvětluje některé základní koncepce za těmito rozhraními API.

> [!WARNING]
> Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.

## <a name="key"></a>Klíč

Rozhraní `IKey` je základní reprezentace klíče v cryptosystem. Výraz používá se tady v abstraktní smysl, ne v literálu smysl "kryptografické klíče". Klíč má následující vlastnosti:

* Aktivace, vytváření a datum vypršení platnosti

* Stav odvolání

* Identifikátor klíče (GUID)

::: moniker range=">= aspnetcore-2.0"

Kromě toho `IKey` zveřejňuje `CreateEncryptor` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Kromě toho `IKey` zveřejňuje `CreateEncryptorInstance` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.

::: moniker-end

> [!NOTE]
> Neexistuje žádné rozhraní API k načtení nezpracovaného kryptografického materiálu z instance `IKey`.

## <a name="ikeymanager"></a>IKeyManager

Rozhraní `IKeyManager` představuje objekt zodpovědný za obecné úložiště klíčů, načítání a manipulaci. Poskytuje tři základní operace:

* Vytvořte nový klíč a trvale uchovávat.

* Získáte všechny klíče ze služby storage.

* Odvolat jeden nebo více klíčů a zachovat informace o odvolání certifikátů do úložiště.

>[!WARNING]
> Zápis `IKeyManager` je velmi pokročilý úkol a většina vývojářů se k tomu nepokouší. Místo toho by měla většina vývojářů využít výhod zařízení nabízených třídou [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

Typ `XmlKeyManager` je v rámci konkrétní implementace `IKeyManager`v krabicích. Poskytuje několik užitečných zařízení, včetně klíčů v úschově a šifrovacích klíčů v klidovém stavu. Klíče v tomto systému jsou reprezentovány jako XML elementy (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` závisí na několika dalších součástech v průběhu plnění úkolů:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, která určuje algoritmy používané novými klíči.

* `IXmlRepository`, které ovládací prvky, ve kterých se ukládají klíče do úložiště.

* `IXmlEncryptor` [volitelné], které povoluje šifrování klíčů v klidovém umístění.

* `IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, které ovládací prvky, ve kterých se ukládají klíče do úložiště.

* `IXmlEncryptor` [volitelné], které povoluje šifrování klíčů v klidovém umístění.

* `IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.

::: moniker-end

Níže jsou uvedeny diagramy vysoké úrovně, které určují, jak jsou tyto komponenty v rámci `XmlKeyManager`propojeny společně.

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

*Vytvoření nebo CreateNewKey klíče*

V implementaci `CreateNewKey`se `AlgorithmConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML. Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení. Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud je to potřeba) pro vygenerování šifrovaného dokumentu XML. Tento zašifrovaný dokument se trvale ukládá do dlouhodobého úložiště prostřednictvím `IXmlRepository`. (Pokud není nakonfigurovaná žádná `IXmlEncryptor`, nezašifrovaný dokument je uložený v `IXmlRepository`.)

![Načítání klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

*Vytvoření nebo CreateNewKey klíče*

V implementaci `CreateNewKey`se `IAuthenticatedEncryptorConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor`, která je poté serializována jako XML. Pokud klíč v úschově jímky je k dispozici, nezpracovaném kódu XML (nešifrovaný) neposkytujeme k jímce pro dlouhodobé uložení. Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud je to potřeba) pro vygenerování šifrovaného dokumentu XML. Tento zašifrovaný dokument se trvale ukládá do dlouhodobého úložiště prostřednictvím `IXmlRepository`. (Pokud není nakonfigurovaná žádná `IXmlEncryptor`, nezašifrovaný dokument je uložený v `IXmlRepository`.)

![Načítání klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

*Načtení klíče/GetAllKeys*

V implementaci `GetAllKeys`jsou dokumenty XML reprezentující klíče a odvolání čteny ze základní `IXmlRepository`. Pokud jsou tyto dokumenty zašifrované, systém bude automaticky jejich dešifrování. `XmlKeyManager` vytvoří příslušné instance `IAuthenticatedEncryptorDescriptorDeserializer` k deserializaci dokumentů zpět do instancí `IAuthenticatedEncryptorDescriptor`, které jsou následně zabaleny do jednotlivých instancí `IKey`. Tato kolekce instancí `IKey` se vrátí volajícímu.

Další informace o konkrétních prvcích XML najdete v [dokumentu formátu úložiště klíčů](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

Rozhraní `IXmlRepository` představuje typ, který může uchovávat XML pro a načíst XML z záložního úložiště. Poskytuje dvě rozhraní API:

* `GetAllElements`:`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementace `IXmlRepository` nepotřebují analyzovat XML předáváním. Jejich považovat za neprůhledné dokumentů XML a umožní vyšší vrstvy starat o generování a analýzu dokumenty.

Existují čtyři předdefinované konkrétní typy, které implementují `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Další informace najdete v [dokumentu Zprostředkovatelé úložiště klíčů](xref:security/data-protection/implementation/key-storage-providers) .

Registrace vlastního `IXmlRepository` je vhodná při použití jiného záložního úložiště (například Azure Table Storage).

Pokud chcete změnit výchozí aplikační úložiště v rámci aplikace, zaregistrujte vlastní instanci `IXmlRepository`:

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

Rozhraní `IXmlEncryptor` představuje typ, který může šifrovat prostý text XML elementu. Poskytuje jediné rozhraní API:

* Šifrování (XElement plaintextElement): EncryptedXmlInfo

Pokud serializovaná `IAuthenticatedEncryptorDescriptor` obsahuje nějaké prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` tyto prvky spustí prostřednictvím nakonfigurované `IXmlEncryptor`metody `Encrypt` a zachová element enciphered namísto elementu prostého textu do `IXmlRepository`. Výstupem metody `Encrypt` je objekt `EncryptedXmlInfo`. Tento objekt je obálka obsahující jak výsledný enciphered `XElement`, tak typ, který představuje `IXmlDecryptor`, které lze použít k dekódování odpovídajícího prvku.

Existují čtyři předdefinované konkrétní typy, které implementují `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Další informace najdete v [dokumentu REST v části šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) .

Chcete-li změnit výchozí klíč-šifrování na úrovni aplikace v klidovém stavu, zaregistrujte vlastní instanci `IXmlEncryptor`:

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

Rozhraní `IXmlDecryptor` představuje typ, který ví, jak dešifrovat `XElement` enciphered prostřednictvím `IXmlEncryptor`. Poskytuje jediné rozhraní API:

* Dešifrování (XElement encryptedElement): XElement

Metoda `Decrypt` odvolá šifrování prováděné `IXmlEncryptor.Encrypt`. Obecně platí, že každá konkrétní implementace `IXmlEncryptor` bude mít odpovídající konkrétní implementaci `IXmlDecryptor`.

Typy, které implementují `IXmlDecryptor` by měly mít jeden z následujících dvou veřejných konstruktorů:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> `IServiceProvider` předaný konstruktoru může mít hodnotu null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

Rozhraní `IKeyEscrowSink` představuje typ, který může provádět v úschově citlivých informací. Odvolání těchto serializovaných popisovačů může obsahovat citlivé údaje (například kryptografický materiál) a to je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) typu na první místo. Však dojít nehodách a aktualizační kanály klíč lze odstranit nebo dojde k poškození.

Rozhraní v úschově poskytuje nouzový řídicí šrafování, který umožňuje přístup k nezpracovanému serializovanému XML před jeho transformací všemi nakonfigurovanými [IXmlEncryptor](#ixmlencryptor). Rozhraní poskytuje jediné rozhraní API:

* Store (keyId identifikátor Guid, XElement element)

Je to až `IKeyEscrowSink` implementace, aby byl poskytnutý prvek bezpečným způsobem konzistentním s obchodními zásadami. Jednou z možných implementací může být, aby jímka v úschově zašifroval XML element pomocí známého podnikového certifikátu X. 509, ve kterém byl privátní klíč certifikátu uloží; typ `CertificateXmlEncryptor` může pomoct s tímto. `IKeyEscrowSink` implementace je také zodpovědná za trvalé uchování poskytnutého prvku.

Ve výchozím nastavení není povolený žádný mechanismus v úschově, i když správci serveru můžou [tuto globálně nakonfigurovat](xref:security/data-protection/configuration/machine-wide-policy). Dá se taky nakonfigurovat programově prostřednictvím metody `IDataProtectionBuilder.AddKeyEscrowSink`, jak je znázorněno v následující ukázce. Metoda `AddKeyEscrowSink` přetížení zrcadlí `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, protože instance `IKeyEscrowSink` mají být typu singleton. Pokud je zaregistrováno více instancí `IKeyEscrowSink`, každá z nich bude volána během generování klíče, takže klíče mohou být uloží k několika mechanismům současně.

Neexistuje žádné rozhraní API pro čtení materiálu z instance `IKeyEscrowSink`. To je konzistentní s návrhu teorie mechanismus v úschově: má určený zpřístupňovaly materiál klíče důvěryhodné autority, a protože aplikace není samotné důvěryhodné autority, by neměl mít přístup k vlastním escrowed materiálu.

Následující vzorový kód ukazuje vytvoření a registraci `IKeyEscrowSink`, kde jsou klíče uloží, aby je mohli obnovit pouze členové "CONTOSODomain Admins".

> [!NOTE]
> Pokud chcete tuto ukázku spustit, musíte být v doméně systému Windows 8 / počítačů Windows serveru 2012 a řadič domény musí být Windows Server 2012 nebo novější.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
