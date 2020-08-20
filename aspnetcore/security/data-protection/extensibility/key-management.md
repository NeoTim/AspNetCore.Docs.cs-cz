---
title: Rozšiřitelnost správy klíčů v ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core rozšiřitelnosti správy klíčů ochrany dat.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 797df457a5584233043210e9ba2657b7fd7f3893
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631001"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Rozšiřitelnost správy klíčů v ASP.NET Core

> [!TIP]
> Před čtením této části si přečtěte část [Správa klíčů](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) , protože vysvětluje některé základní koncepce za těmito rozhraními API.

> [!WARNING]
> Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.

## <a name="key"></a>Klíč

`IKey`Rozhraní je základní reprezentace klíče v cryptosystem. Pojem Key se tady používá v abstraktním smyslu, nikoli v literálu "materiál kryptografického klíče". Klíč má následující vlastnosti:

* Data o aktivaci, vytváření a vypršení platnosti

* Stav odvolání

* Identifikátor klíče (identifikátor GUID)

::: moniker range=">= aspnetcore-2.0"

Navíc `IKey` zpřístupňuje `CreateEncryptor` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Navíc `IKey` zpřístupňuje `CreateEncryptorInstance` metodu, která se dá použít k vytvoření instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) svázané s tímto klíčem.

::: moniker-end

> [!NOTE]
> Neexistuje žádné rozhraní API pro načtení nezpracovaného kryptografického materiálu z `IKey` instance.

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager`Rozhraní představuje objekt zodpovědný za obecné úložiště klíčů, načítání a manipulaci. Zveřejňuje tři operace vysoké úrovně:

* Vytvořte nový klíč a zachovejte ho do úložiště.

* Získá všechny klíče z úložiště.

* Odvolejte jeden nebo více klíčů a zachovejte informace o odvolání úložiště.

>[!WARNING]
> Psaní `IKeyManager` je velmi pokročilý úkol a většina vývojářů ji nezkouší. Místo toho by měla většina vývojářů využít výhod zařízení nabízených třídou [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager`Typ je konkrétní implementace `IKeyManager` . Nabízí několik užitečných zařízení, včetně služby Key v úschově a šifrování neaktivních klíčů. Klíče v tomto systému jsou reprezentovány jako XML elementy (konkrétně [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` závisí na několika dalších součástech v průběhu plnění úkolů:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, který určuje algoritmy používané novými klíči.

* `IXmlRepository`, které řídí, kde jsou klíče uchovávány v úložišti.

* `IXmlEncryptor` [volitelné], což umožňuje šifrování klíčů v klidovém umístění.

* `IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, které řídí, kde jsou klíče uchovávány v úložišti.

* `IXmlEncryptor` [volitelné], což umožňuje šifrování klíčů v klidovém umístění.

* `IKeyEscrowSink` [volitelné], které poskytuje službu Key v úschově Services.

::: moniker-end

Níže jsou uvedeny diagramy vysoké úrovně, které určují, jak jsou tyto komponenty v rámci společné `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation2.png)

*Vytvoření nebo CreateNewKey klíče*

V implementaci nástroje se `CreateNewKey` `AlgorithmConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor` , která je poté serializována jako XML. Pokud je přítomná jímka služby Key v úschově, nezpracovaný (nešifrovaný) kód XML je poskytnut jímka pro dlouhodobé uložení. Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud se vyžaduje) pro vygenerování šifrovaného dokumentu XML. Tento zašifrovaný dokument je trvalý v dlouhodobém úložišti prostřednictvím `IXmlRepository` . (Pokud `IXmlEncryptor` není nakonfigurováno, nešifrovaný dokument je uložen v `IXmlRepository` .)

![Načtení klíče](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Vytvoření klíče](key-management/_static/keycreation1.png)

*Vytvoření nebo CreateNewKey klíče*

V implementaci nástroje se `CreateNewKey` `IAuthenticatedEncryptorConfiguration` komponenta používá k vytvoření jedinečné `IAuthenticatedEncryptorDescriptor` , která je poté serializována jako XML. Pokud je přítomná jímka služby Key v úschově, nezpracovaný (nešifrovaný) kód XML je poskytnut jímka pro dlouhodobé uložení. Nešifrovaný kód XML se pak spustí prostřednictvím `IXmlEncryptor` (Pokud se vyžaduje) pro vygenerování šifrovaného dokumentu XML. Tento zašifrovaný dokument je trvalý v dlouhodobém úložišti prostřednictvím `IXmlRepository` . (Pokud `IXmlEncryptor` není nakonfigurováno, nešifrovaný dokument je uložen v `IXmlRepository` .)

![Načtení klíče](key-management/_static/keyretrieval1.png)

::: moniker-end

*Načtení klíče/GetAllKeys*

V implementaci nástroje `GetAllKeys` jsou dokumenty XML reprezentující klíče a odvolání čteny ze základní `IXmlRepository` . Pokud jsou tyto dokumenty šifrovány, systém je bude automaticky dešifroval. `XmlKeyManager` Vytvoří vhodné `IAuthenticatedEncryptorDescriptorDeserializer` instance pro rekonstrukci dokumentů zpět do `IAuthenticatedEncryptorDescriptor` instancí, které jsou poté zabaleny do jednotlivých `IKey` instancí. Tato kolekce `IKey` instancí se vrátí volajícímu.

Další informace o konkrétních prvcích XML najdete v [dokumentu formátu úložiště klíčů](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository`Rozhraní představuje typ, který může uchovávat XML a načíst XML z záložního úložiště. Zpřístupňuje dvě rozhraní API:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementace `IXmlRepository` nepotřebují analyzovat kód XML, který je projde. Měly by se dokumenty XML nacházet jako neprůhledné a umožňují rychlejší vytváření a analýzu dokumentů.

Existují čtyři předdefinované konkrétní typy, které implementují `IXmlRepository` :

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

Vlastní registrace `IXmlRepository` je vhodná při použití jiného záložního úložiště (například Azure Table Storage).

Pokud chcete změnit výchozí aplikační úložiště v rámci aplikace, zaregistrujte si vlastní `IXmlRepository` instanci:

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

`IXmlEncryptor`Rozhraní představuje typ, který může šifrovat prostý text XML elementu. Zpřístupňuje jedno rozhraní API:

* Šifrovat (XElement plaintextElement): EncryptedXmlInfo

Pokud serializovaná `IAuthenticatedEncryptorDescriptor` obsahuje nějaké prvky označené jako "vyžaduje šifrování", pak `XmlKeyManager` tyto prvky spustí prostřednictvím nakonfigurované `IXmlEncryptor` `Encrypt` metody a zachová element enciphered, nikoli elementu prostého textu do `IXmlRepository` . Výstupem `Encrypt` metody je `EncryptedXmlInfo` objekt. Tento objekt je obálkou, která obsahuje výsledný enciphered `XElement` a typ, který představuje, `IXmlDecryptor` který lze použít k dekódování odpovídajícího prvku.

Existují čtyři předdefinované konkrétní typy, které implementují `IXmlEncryptor` :

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Další informace najdete v [dokumentu REST v části šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) .

Pro změnu výchozího mechanismu šifrování klíče v rámci aplikace na úrovni Standard, zaregistrujte vlastní `IXmlEncryptor` instanci:

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

`IXmlDecryptor`Rozhraní představuje typ, který zná způsob dešifrování `XElement` , které bylo enciphered prostřednictvím `IXmlEncryptor` . Zpřístupňuje jedno rozhraní API:

* Dešifrovat (XElement encryptedElement): XElement

`Decrypt`Metoda odvolá šifrování prováděné `IXmlEncryptor.Encrypt` . Obecně platí, že každá konkrétní `IXmlEncryptor` implementace bude mít odpovídající konkrétní `IXmlDecryptor` implementaci.

Typy, které implementují, `IXmlDecryptor` by měly mít jeden z následujících dvou veřejných konstruktorů:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> `IServiceProvider`Předaný konstruktor může mít hodnotu null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink`Rozhraní představuje typ, který může provádět v úschově citlivých informací. Odvolání těchto serializovaných popisovačů může obsahovat citlivé údaje (například kryptografický materiál) a to je to, co vedlo k zavedení [IXmlEncryptor](#ixmlencryptor) typu na první místo. Nicméně dojde k nehodám a zazvonění klíčů lze odstranit nebo poškodit.

Rozhraní v úschově poskytuje nouzový řídicí šrafování, který umožňuje přístup k nezpracovanému serializovanému XML před jeho transformací všemi nakonfigurovanými [IXmlEncryptor](#ixmlencryptor). Rozhraní zpřístupňuje jedno rozhraní API:

* Store (GUID keyId, XElement element)

Je to až do `IKeyEscrowSink` implementace, aby byl poskytnutý prvek bezpečným způsobem konzistentním s obchodními zásadami. Jednou z možných implementací může být, aby jímka v úschově zašifroval XML element pomocí známého podnikového certifikátu X. 509, ve kterém byl privátní klíč certifikátu uloží; `CertificateXmlEncryptor` Tento typ může pomoct s tímto. `IKeyEscrowSink`Implementace je také zodpovědná za trvalé uchování poskytnutého prvku.

Ve výchozím nastavení není povolený žádný mechanismus v úschově, i když správci serveru můžou [tuto globálně nakonfigurovat](xref:security/data-protection/configuration/machine-wide-policy). Lze ji také programově nakonfigurovat prostřednictvím `IDataProtectionBuilder.AddKeyEscrowSink` metody, jak je znázorněno v následující ukázce. `AddKeyEscrowSink`Metoda přetěžuje zrcadlení `IServiceCollection.AddSingleton` a `IServiceCollection.AddInstance` přetížení, protože `IKeyEscrowSink` instance mají být typu singleton. Pokud `IKeyEscrowSink` je registrováno více instancí, každá z nich bude volána během generování klíče, takže klíče mohou být uloží k několika mechanismům současně.

Neexistuje žádné rozhraní API pro čtení materiálu z `IKeyEscrowSink` instance. To je v souladu s tabulkou pro návrh v úschově mechanismu: je určena k tomu, aby byl materiál klíče přístupný pro důvěryhodnou autoritu a protože aplikace sám o sobě není důvěryhodnou autoritou, neměla by mít přístup ke svému vlastnímu uloží materiálu.

Následující vzorový kód ukazuje vytvoření a registraci `IKeyEscrowSink` klíčů, kde jsou uloží, aby je mohli obnovit pouze členové "CONTOSODomain Admins".

> [!NOTE]
> Pokud chcete tuto ukázku spustit, musíte být na počítači se systémem Windows 8/Windows Server 2012 připojeným k doméně a řadičem domény musí být Windows Server 2012 nebo novější.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
