---
title: Rozšiřitelnost základní kryptografie v ASP.NET Core
author: rick-anderson
description: Další informace o IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer a objekt pro vytváření nejvyšší úrovně.
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814695"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Rozšiřitelnost základní kryptografie v ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typy, které implementují některý z následujících rozhraní by měly být bezpečné pro vlákna pro více volání.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor** rozhraní je základním stavebním blokem šifrovací subsystém. Obecně je jeden IAuthenticatedEncryptor za klíč a IAuthenticatedEncryptor instance zabalí všechny kryptografické klíče a vylepšením informace potřebné k provedení kryptografických operací.

Jak název napovídá, typ je odpovědný za poskytnutí ověřeného šifrovacích a dešifrovacích služeb. Poskytuje následující dvě rozhraní API.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Vrátí metoda šifrování objektů blob, který zahrnuje enciphered ve formátu prostého textu a ověřovací značka. Ověřovací značka musí zahrnovat další ověřených dat (AAD), i když AAD, samotný nemusí být obnovitelné z poslední datové části. Metoda dešifrovat ověří ověřovací značka a vrátí deciphered datové části. K cryptographicexception – musí být homogenizovány všechny chyby (s výjimkou ArgumentNullException a podobně).

> [!NOTE]
> Samotné instanci IAuthenticatedEncryptor ve skutečnosti nemusí obsahovat materiál klíče. Například může delegovat implementace modulu hardwarového zabezpečení pro všechny operace.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Vytvoření IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorFactory** rozhraní představuje typ, který se ví, jak vytvořit [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance. Své rozhraní API je následujícím způsobem.

* CreateEncryptorInstance (klíč Instrumentační klíč): IAuthenticatedEncryptor

Pro jakoukoli danou instanci Instrumentační klíč, všechny ověřené encryptors vytvářené metodou jeho CreateEncryptorInstance by měl být ekvivalentní, stejně jako následující vzorový kód.

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorDescriptor** rozhraní představuje typ, který se ví, jak vytvořit [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance. Své rozhraní API je následujícím způsobem.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Stejně jako IAuthenticatedEncryptor instance IAuthenticatedEncryptorDescriptor je považován za zabalení jeden konkrétní klíč. To znamená, že pro všechny instance daného IAuthenticatedEncryptorDescriptor všechny ověřené encryptors vytvářené metodou jeho CreateEncryptorInstance by měl být považovány za ekvivalentní, stejně jako v následující vzorový kód.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x jenom)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorDescriptor** rozhraní představuje typ, který umí samotné exportovat do formátu XML. Své rozhraní API je následujícím způsobem.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serializace XML

Hlavní rozdíl mezi IAuthenticatedEncryptor a IAuthenticatedEncryptorDescriptor je, že popisovač ví, jak vytvořit šifrování a poskytne mu platné argumenty. Vezměte v úvahu IAuthenticatedEncryptor, jehož implementace využívá SymmetricAlgorithm a KeyedHashAlgorithm. Úloha šifrování je používat tyto typy, ale nemá specifické znalosti nutně těchto typů, odkud, takže ho nejde vypsat skutečně správné popis toho, jak vytvořit samotné znovu, pokud se aplikace restartuje. Popisovač funguje jako vyšší úroveň nad to. Vzhledem k tomu, že popisovač ví, jak vytvořit instanci šifrování (například ví, jak vytvořit požadované algoritmy), tak, aby modul instance může být znovu vytvořena po obnovení aplikace ji může serializovat dané znalosti v podobě XML.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Popisovač lze serializovat pomocí jeho ExportToXml rutiny. Tato rutina vrátí XmlSerializedDescriptorInfo, která obsahuje dvě vlastnosti: XElement reprezentace popisovač a typ, který představuje [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) může být použít resurrect tento popisovač zadaný odpovídající XElement.

Serializovaná popisovače mohou obsahovat citlivé informace, jako je například kryptografické klíče. Systém ochrany dat obsahuje integrovanou podporu pro šifrování informací, než se trvale uložena do úložiště. Umožní využít této popisovač by měl označit element, který obsahuje citlivé informace pomocí atributu name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), hodnota "true".

>[!TIP]
> Nastavení tohoto atributu je pomocná rozhraní API. Volání metody rozšíření, které XElement.MarkAsRequiresEncryption() nachází v oboru názvů Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Může existovat také případy, kdy serializovaná popisovač neobsahuje citlivé informace. Podívejte se znovu na kryptografického klíče uložené v modulu hardwarového zabezpečení. Popisovač nelze vypsat materiál klíče při serializaci samotné, protože modul hardwarového zabezpečení nebude vystavit materiál v podobě prostého textu. Místo toho může být popisovač vypsat klíč zabalené verze klíče (Pokud modul hardwarového zabezpečení umožňuje export tímto způsobem) nebo hardwarového zabezpečení pro vlastní jedinečný identifikátor pro klíč.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer** rozhraní představuje typ, který se ví, jak provést deserializaci instance IAuthenticatedEncryptorDescriptor z XElement. Poskytuje jedinou metodu:

* ImportFromXml (XElement element): IAuthenticatedEncryptorDescriptor

Metoda ImportFromXml přebírá XElement, který byl vrácen [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) a vytvoří ekvivalent původní IAuthenticatedEncryptorDescriptor.

Typy, které implementují IAuthenticatedEncryptorDescriptorDeserializer musí mít jednu z následujících dvou veřejných konstruktorů:

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> IServiceProvider předaný konstruktoru může mít hodnotu null.

## <a name="the-top-level-factory"></a>Objekt pro vytváření nejvyšší úrovně

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration** třída představuje typ, který umí vytvořit [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instancí. Poskytuje jediné rozhraní API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

AlgorithmConfiguration můžete představit jako objekt pro vytváření nejvyšší úrovně. Konfigurace slouží jako šablona. Zabalí vylepšením informace (například tato konfigurace vytvoří popisovače pomocí hlavního klíče AES-128-GCM), ale je ještě není přidružená určitého klíče.

Když je zavolána CreateNewDescriptor, výhradně pro toto volání se vytvoří nový materiál klíče a je vytvořen nový IAuthenticatedEncryptorDescriptor který zabalí tento materiál klíče a vylepšením informace požadované pro využívání materiálu. Materiál klíče může vytvořit v softwaru (a uložené v paměti), může být vytvořen a uložených v HSM a tak dále. Velmi důležitý bod je, že dvě volání CreateNewDescriptor nikdy vytvořit ekvivalentní IAuthenticatedEncryptorDescriptor instancí.

Typ AlgorithmConfiguration slouží jako vstupní bod pro vytvoření klíče rutin, jako [automatické klíč se zajištěním provozu](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Chcete-li změnit implementace pro všechny budoucí klíče, nastavte vlastnost AuthenticatedEncryptorConfiguration v KeyManagementOptions.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorConfiguration** rozhraní představuje typ, který umí vytvořit [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instancí. Poskytuje jediné rozhraní API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

IAuthenticatedEncryptorConfiguration můžete představit jako objekt pro vytváření nejvyšší úrovně. Konfigurace slouží jako šablona. Zabalí vylepšením informace (například tato konfigurace vytvoří popisovače pomocí hlavního klíče AES-128-GCM), ale je ještě není přidružená určitého klíče.

Když je zavolána CreateNewDescriptor, výhradně pro toto volání se vytvoří nový materiál klíče a je vytvořen nový IAuthenticatedEncryptorDescriptor který zabalí tento materiál klíče a vylepšením informace požadované pro využívání materiálu. Materiál klíče může vytvořit v softwaru (a uložené v paměti), může být vytvořen a uložených v HSM a tak dále. Velmi důležitý bod je, že dvě volání CreateNewDescriptor nikdy vytvořit ekvivalentní IAuthenticatedEncryptorDescriptor instancí.

Typ IAuthenticatedEncryptorConfiguration slouží jako vstupní bod pro vytvoření klíče rutin, jako [automatické klíč se zajištěním provozu](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Chcete-li změnit implementace pro všechny budoucí klíče, zaregistrujte IAuthenticatedEncryptorConfiguration jednotlivý prvek v kontejneru služby.

---
