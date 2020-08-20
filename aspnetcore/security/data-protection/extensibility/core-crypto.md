---
title: Rozšiřitelnost základního kryptografie v ASP.NET Core
author: rick-anderson
description: Seznamte se s IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer a továrnou nejvyšší úrovně.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 4c802bc4beb1f1fde812e6c3f55fc43b5d569b66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635317"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Rozšiřitelnost základního kryptografie v ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typy, které implementují některá z následujících rozhraní, by měly být bezpečné pro přístup z více vláken pro více volajících.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

Rozhraní **IAuthenticatedEncryptor** je základním stavebním blokem kryptografického subsystému. Na klíč je všeobecně jedna IAuthenticatedEncryptor a instance IAuthenticatedEncryptor balí všechny materiály kryptografického klíče a algoritmy, které jsou nezbytné k provádění kryptografických operací.

Vzhledem k tomu, že název navrhuje, zodpovídá za poskytování ověřených šifrovacích a dešifrovacích služeb. Zpřístupňuje následující dvě rozhraní API.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Metoda Encrypt vrátí objekt blob, který obsahuje enciphered prostý text a ověřovací značku. Ověřovací značka musí zahrnovat další ověřená data (AAD), i když se samotný AAD nemusí z poslední datové části zotavit. Metoda dešifrer ověří ověřovací značku a vrátí dešifrovanou datovou část. Všechny chyby (s výjimkou ArgumentNullException a podobných) by měly být homogenní na CryptographicException –.

> [!NOTE]
> Samotná instance IAuthenticatedEncryptor ve skutečnosti nemusí obsahovat klíč klíče. Například implementace by mohla delegovat na modul HARDWAROVÉho zabezpečení pro všechny operace.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Vytvoření IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Rozhraní **IAuthenticatedEncryptorFactory** představuje typ, který ví, jak vytvořit instanci [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Jeho rozhraní API je následující.

* CreateEncryptorInstance (klíč IKey): IAuthenticatedEncryptor

U všech daných instancí IKey by měly být všechny ověřené šifry vytvořené jeho metodou CreateEncryptorInstance považovány za ekvivalentní, jako v níže uvedené ukázce kódu.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

Rozhraní **IAuthenticatedEncryptorDescriptor** představuje typ, který ví, jak vytvořit instanci [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Jeho rozhraní API je následující.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Podobně jako IAuthenticatedEncryptor se předpokládá, že instance IAuthenticatedEncryptorDescriptor zabalí jeden konkrétní klíč. To znamená, že u všech daných instancí IAuthenticatedEncryptorDescriptor by měly být všechny ověřené šifry vytvořené jeho metodou CreateEncryptorInstance považovány za ekvivalentní, jako v níže uvedené ukázce kódu.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (pouze ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Rozhraní **IAuthenticatedEncryptorDescriptor** představuje typ, který ví, jak se má exportovat do XML. Jeho rozhraní API je následující.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serializace XML

Hlavním rozdílem mezi IAuthenticatedEncryptor a IAuthenticatedEncryptorDescriptor je, že popisovač ví, jak vytvořit modul pro šifrování a dodat jej s platnými argumenty. Vezměte v úvahu IAuthenticatedEncryptor, jehož implementace spoléhá na SymmetricAlgorithm a KeyedHashAlgorithm. Úloha zašifry je určena ke využívání těchto typů, ale nemusí nutně znát, odkud pocházejí tyto typy, takže nemůže skutečně napsat řádný popis způsobu, jak je znovu vytvořit, pokud se aplikace restartuje. Popisovač funguje jako vyšší úroveň nad tímto. Vzhledem k tomu, že popisovač ví, jak vytvořit instanci služby šifry (například ví, jak vytvořit požadované algoritmy), může toto znalosti ve formuláři XML serializovat, aby se instance šifry mohla znovu vytvořit po resetování aplikace.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Popisovač lze serializovat prostřednictvím jeho rutiny ExportToXml. Tato rutina vrátí XmlSerializedDescriptorInfo, který obsahuje dvě vlastnosti: XElement reprezentace popisovače a typ, který představuje [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) , který lze použít k obnovení tohoto deskriptoru, který má přidělen odpovídající XElement.

Serializovaný deskriptor může obsahovat citlivé informace, jako je například materiál kryptografického klíče. Systém ochrany dat má integrovanou podporu pro šifrování informací předtím, než je trvale uložený do úložiště. Chcete-li tuto funkci využít, deskriptor by měl označit element, který obsahuje citlivé informace s názvem atributu "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> "), hodnotou "true".

>[!TIP]
> Pro nastavení tohoto atributu je k dispozici pomocné rozhraní API. Zavolejte metodu rozšíření XElement. MarkAsRequiresEncryption () umístěnou v oboru názvů Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Mohou existovat také případy, kdy serializovaný popisovač neobsahuje citlivé informace. Znovu zvažte případ kryptografického klíče uloženého v modulu hardwarového zabezpečení (HSM). Deskriptor nemůže zapsat klíč klíče při serializaci sebe sama, protože modul HARDWAROVÉho zabezpečení nezveřejňuje materiál ve formě prostého textu. Místo toho může popisovač zapsat verzi klíče zabaleného klíče (Pokud modul HARDWAROVÉho zabezpečení umožňuje export tímto způsobem) nebo vlastní jedinečný identifikátor HSM pro klíč.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

Rozhraní **IAuthenticatedEncryptorDescriptorDeserializer** představuje typ, který ví, jak rekonstruovat instanci IAuthenticatedEncryptorDescriptor z XElement. Zpřístupňuje jednu metodu:

* ImportFromXml (XElement element): IAuthenticatedEncryptorDescriptor

Metoda ImportFromXml přijímá XElement vrácenou funkcí [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) a vytvoří ekvivalent původní IAuthenticatedEncryptorDescriptor.

Typy, které implementují IAuthenticatedEncryptorDescriptorDeserializer, by měly mít jeden z následujících dvou veřejných konstruktorů:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> Objekt IServiceProvider předaný konstruktoru může mít hodnotu null.

## <a name="the-top-level-factory"></a>Továrna nejvyšší úrovně

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

Třída **AlgorithmConfiguration** představuje typ, který ví, jak vytvořit instance [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Zpřístupňuje jedno rozhraní API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

AlgorithmConfiguration se považujte za továrnu nejvyšší úrovně. Konfigurace slouží jako šablona. Zalomí informace o algoritmech (např. Tato konfigurace vytvoří popisovače pomocí GCM hlavního klíče AES-128), ale ještě není přidružená k určitému klíči.

Když se zavolá CreateNewDescriptor, vytvoří se nový klíčový materiál výhradně pro toto volání a vytvoří se nový IAuthenticatedEncryptorDescriptor, který zabalí tento klíčový materiál a algoritmy, které jsou nutné k využívání materiálu. Klíčový materiál může být vytvořen v softwaru (a uložený v paměti), mohl by být vytvořen a uložený v modulu HARDWAROVÉho zabezpečení a tak dále. Rozhodujícím bodem je, že jakákoli dvě volání CreateNewDescriptor by nikdy neměla vytvářet ekvivalentní instance IAuthenticatedEncryptorDescriptor.

Typ AlgorithmConfiguration slouží jako vstupní bod pro rutiny vytváření klíčů, jako je například [automatické zavedení klíče](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Chcete-li změnit implementaci pro všechny budoucí klíče, nastavte vlastnost AuthenticatedEncryptorConfiguration v KeyManagementOptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

Rozhraní **IAuthenticatedEncryptorConfiguration** představuje typ, který ví, jak vytvořit instance [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Zpřístupňuje jedno rozhraní API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

IAuthenticatedEncryptorConfiguration se považujte za továrnu nejvyšší úrovně. Konfigurace slouží jako šablona. Zalomí informace o algoritmech (např. Tato konfigurace vytvoří popisovače pomocí GCM hlavního klíče AES-128), ale ještě není přidružená k určitému klíči.

Když se zavolá CreateNewDescriptor, vytvoří se nový klíčový materiál výhradně pro toto volání a vytvoří se nový IAuthenticatedEncryptorDescriptor, který zabalí tento klíčový materiál a algoritmy, které jsou nutné k využívání materiálu. Klíčový materiál může být vytvořen v softwaru (a uložený v paměti), mohl by být vytvořen a uložený v modulu HARDWAROVÉho zabezpečení a tak dále. Rozhodujícím bodem je, že jakákoli dvě volání CreateNewDescriptor by nikdy neměla vytvářet ekvivalentní instance IAuthenticatedEncryptorDescriptor.

Typ IAuthenticatedEncryptorConfiguration slouží jako vstupní bod pro rutiny vytváření klíčů, jako je například [automatické zavedení klíče](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Pokud chcete změnit implementaci pro všechny budoucí klíče, zaregistrujte v kontejneru služby IAuthenticatedEncryptorConfiguration typu singleton.

---
