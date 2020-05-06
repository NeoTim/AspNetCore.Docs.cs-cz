---
title: Konfigurace ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si, jak nakonfigurovat ochranu dat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: d37fdc44bb9a603d85818fc72a7a07de67006366
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776809"
---
# <a name="configure-aspnet-core-data-protection"></a>Konfigurace ochrany ASP.NET Core dat

Po inicializaci systému ochrany dat se použije [výchozí nastavení](xref:security/data-protection/configuration/default-settings) na základě operačního prostředí. Tato nastavení jsou obecně vhodná pro aplikace běžící na jednom počítači. V některých případech může vývojář chtít změnit výchozí nastavení:

* Aplikace je rozdělená mezi několik počítačů.
* Z důvodu dodržování předpisů.

V těchto scénářích nabízí systém ochrany dat bohatá rozhraní API pro konfiguraci.

> [!WARNING]
> Podobně jako u konfiguračních souborů by měl být datový Prstenový kanál ochrany dat chráněný pomocí příslušných oprávnění. Můžete zvolit šifrování klíčů v klidovém umístění, ale nebrání útočníkům v vytváření nových klíčů. V důsledku toho je zabezpečení vaší aplikace ovlivněno. Umístění úložiště nakonfigurované s ochranou dat by mělo mít přístup omezený jenom na samotnou aplikaci, podobně jako při ochraně konfiguračních souborů. Pokud se například rozhodnete, že budete svůj klíč Ring ukládat na disk, použijte oprávnění systému souborů. Zajistěte, aby byla ve vaší webové aplikaci při čtení, zápisu a vytváření přístupu k tomuto adresáři pouze identita, pod kterou vaše webová aplikace běží. Pokud používáte Azure Blob Storage, měla by mít možnost číst, zapisovat nebo vytvářet nové položky v úložišti objektů BLOB atd.
>
> Metoda rozšíření [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) vrátí [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`Zpřístupňuje metody rozšíření, které lze řetězit ke konfiguraci možností ochrany dat.

::: moniker range=">= aspnetcore-3.0"

Pro rozšíření ochrany dat používané v tomto článku jsou vyžadovány následující balíčky NuGet:

* [Microsoft. AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [Microsoft. AspNetCore. DataProtection. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Pokud chcete ukládat klíče v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém pomocí [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ve `Startup` třídě:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Nastavte umístění úložiště Key Ring (například [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Umístění musí být nastavené, protože `ProtectKeysWithAzureKeyVault` volání implementuje rozhraní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , které zakazuje automatickou konfiguraci ochrany dat, včetně umístění úložiště s klíčovým kroužkem. Předchozí příklad používá službu Azure Blob Storage k uchování služby Key Ring. Další informace najdete v tématu [Zprostředkovatelé úložiště klíčů: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Klíčového ringu můžete také zachovat místně pomocí [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier` Je identifikátor klíče trezoru klíčů, který se používá pro šifrování klíče. Například klíč vytvořený v trezoru klíčů s názvem `dataprotection` `contosokeyvault` obsahuje identifikátor `https://contosokeyvault.vault.azure.net/keys/dataprotection/`klíče. Poskytněte aplikaci oprávnění k **rozbalení klíče** a **zabalení klíče** do trezoru klíčů.

`ProtectKeysWithAzureKeyVault`přetížení

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) umožňuje použití [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) k tomu, aby systém ochrany dat mohl používat Trezor klíčů.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) umožňuje použití `ClientId` a [certifikátu x509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) k tomu, aby systém ochrany dat mohl používat Trezor klíčů.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) povolí použití `ClientId` a `ClientSecret` k povolení systému ochrany dat pro použití trezoru klíčů.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Chcete-li uložit klíče ve sdílené složce UNC místo ve výchozím umístění *% localappdata%* , nakonfigurujte systém pomocí [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Pokud změníte umístění trvalosti klíčů, systém už nebude automaticky šifrovat klíče v klidovém režimu, protože neví, jestli je DPAPI vhodný šifrovací mechanismus.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Systém můžete nakonfigurovat tak, aby chránil klíče v klidovém umístění voláním libovolného rozhraní API pro konfiguraci [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Vezměte v úvahu následující příklad, který ukládá klíče do sdílené složky UNC a šifruje tyto klíče v klidovém formátu pomocí konkrétního certifikátu X. 509:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

V ASP.NET Core 2,1 nebo novějším můžete zadat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), jako je například certifikát načtený ze souboru:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

Další příklady a diskuze o integrovaných mechanismech šifrování klíčů najdete v tématu [šifrování klíčů v klidovém umístění](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

V ASP.NET Core 2,1 nebo novějších můžete otáčet certifikáty a dešifrovací klíče v klidovém formátu pomocí pole [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certifikátů s [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Pokud chcete systém nakonfigurovat tak, aby používal životnost klíče 14 dní namísto výchozích 90 dní, použijte [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Ve výchozím nastavení systém ochrany dat izoluje aplikace od sebe navzájem na základě jejich [kořenových cest obsahu](xref:fundamentals/index#content-root) , i když sdílí stejné fyzické úložiště klíčů. To aplikacím brání v porozumění chráněným datovým částem.

Sdílení chráněných datových částí mezi aplikacemi:

* Nakonfigurujte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> v každé aplikaci stejnou hodnotu.
* Používejte stejnou verzi Data Protection API stacku napříč aplikacemi. V souborech projektu aplikace proveďte **jednu** z následujících akcí:
  * Odkázat na stejnou verzi sdíleného rozhraní prostřednictvím [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).
  * Odkaz na stejnou verzi [balíčku ochrany dat](xref:security/data-protection/introduction#package-layout)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Můžete mít situaci, kdy nechcete, aby aplikace automaticky nastavila klíče (vytváření nových klíčů) při přístupu k vypršení platnosti. Příkladem může být aplikace nastavovaná v relaci primárního a sekundárního, kde pouze primární aplikace zodpovídá za důležité aspekty správy klíčů a sekundární aplikace má pouze pohled jen pro čtení ve službě Key Ring. Sekundární aplikace je možné nakonfigurovat tak, aby považovaly klíčový prstenec za jen pro čtení, a to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>konfigurací systému pomocí:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Izolace podle aplikace

Když je systém ochrany dat poskytovaný ASP.NET Core hostitelem, automaticky izoluje aplikace od sebe navzájem, i když tyto aplikace běží pod stejným účtem pracovního procesu a používají stejný hlavní materiál klíčů. To je trochu podobné modifikátoru IsolateApps z `<machineKey>` prvku System. Web.

Mechanismus izolace funguje tak, že zvažuje každou aplikaci v místním počítači jako jedinečného tenanta, takže <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> kořenová složka pro všechny dané aplikace automaticky zahrnuje ID aplikace jako diskriminátor. Jedinečným ID aplikace je fyzická cesta aplikace:

* Pro aplikace hostované ve službě IIS je jedinečným ID fyzická cesta k aplikaci služby IIS. Pokud je aplikace nasazená ve webové farmě, je tato hodnota stabilní za předpokladu, že prostředí služby IIS se konfigurují podobně napříč všemi počítači ve webové farmě.
* Pro samoobslužné aplikace běžící na [serveru Kestrel](xref:fundamentals/servers/index#kestrel)je jedinečné ID fyzickou cestou k aplikaci na disku.

Jedinečný identifikátor je navržený tak, aby se&mdash;předržel jak jednotlivé aplikace, tak i samotný počítač.

Tento mechanismus izolace předpokládá, že aplikace nejsou škodlivé. Škodlivá aplikace může vždycky ovlivnit jakoukoli jinou aplikaci spuštěnou v rámci stejného účtu pracovního procesu. Ve sdíleném hostitelském prostředí, kde jsou aplikace vzájemně nedůvěryhodné, by měl poskytovatel hostingu podniknout kroky k zajištění izolace na úrovni operačního systému mezi aplikacemi, včetně oddělení základních úložišť klíčů aplikací.

Pokud není systém ochrany dat poskytovaný ASP.NET Corem hostitelem (například při vytvoření instance prostřednictvím `DataProtectionProvider` konkrétního typu), je ve výchozím nastavení zakázána izolace aplikace. Pokud je izolace aplikace zakázaná, můžou všechny aplikace, které používá stejný materiál klíče, sdílet datové části, pokud poskytují vhodné [účely](xref:security/data-protection/consumer-apis/purpose-strings). Pokud chcete v tomto prostředí poskytnout izolaci aplikace, zavolejte metodu [SetApplicationName](#setapplicationname) na objekt konfigurace a zadejte jedinečný název pro každou aplikaci.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Změna algoritmů pomocí UseCryptographicAlgorithms

Zásobník ochrany dat umožňuje změnit výchozí algoritmus používaný nově generovanými klíči. Nejjednodušší způsob, jak to provést, je volat [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) z zpětného volání konfigurace:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

Výchozí EncryptionAlgorithm je AES-256-CBC a výchozí ValidationAlgorithm je HMACSHA256. Výchozí zásady může nastavit správce systému pomocí [zásad pro celé počítače](xref:security/data-protection/configuration/machine-wide-policy), ale explicitní volání `UseCryptographicAlgorithms` přepisu výchozích zásad.

Volání `UseCryptographicAlgorithms` umožňuje zadat požadovaný algoritmus z předdefinovaného předdefinovaného seznamu. Nemusíte si dělat starosti s implementací algoritmu. Ve výše uvedeném scénáři se systém ochrany dat při spuštění ve Windows pokusí použít implementaci CNG pro AES. V opačném případě se vrátí do spravované třídy [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

Implementaci můžete zadat ručně prostřednictvím volání [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Změna algoritmů neovlivní existující klíče ve službě Key Ring. Ovlivňuje jenom nově vygenerované klíče.

### <a name="specifying-custom-managed-algorithms"></a>Určení vlastních spravovaných algoritmů

::: moniker range=">= aspnetcore-2.0"

Chcete-li zadat vlastní spravované algoritmy, vytvořte instanci [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , která odkazuje na typy implementace:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Chcete-li zadat vlastní spravované algoritmy, vytvořte instanci [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , která odkazuje na typy implementace:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

Obecně musí \*být vlastnosti typu odkazovány na konkrétní instantiable (prostřednictvím neveřejných konstruktorů konstruktoru bez parametrů) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) a [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), i když systémové speciální případy některé hodnoty jako `typeof(Aes)` pro pohodlí.

> [!NOTE]
> SymmetricAlgorithm musí mít délku klíče s ≥ 128 bity a blokovou velikostí ≥ 64 bitů a musí podporovat šifrování v režimu CBC s odsazením PKCS #7. KeyedHashAlgorithm musí mít velikost Digest >= 128 bitů a musí podporovat klíče délky rovnající se délce Digest algoritmu hash. KeyedHashAlgorithm není striktně vyžadováno pro HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Určení vlastních algoritmů služby Windows CNG

::: moniker range=">= aspnetcore-2.0"

Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování v režimu CBC s ověřováním HMAC, vytvořte instanci [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) obsahující informace o algoritmech:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování v režimu CBC s ověřováním HMAC, vytvořte instanci [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) obsahující informace o algoritmech:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> Algoritmus symetrického šifrování bloku musí mít délku klíče >= 128 bitů, velikost bloku >= 64 bitů a musí podporovat šifrování v CBC s odsazením PKCS #7. Algoritmus hash musí mít velikost algoritmu Digest >= 128 bitů a musí podporovat otevření pomocí příznaku HMAC\_\_ALG\_popisovače\_symetrickými. Vlastnosti \*zprostředkovatele lze nastavit na hodnotu null pro použití výchozího zprostředkovatele pro zadaný algoritmus. Další informace najdete v dokumentaci k [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

::: moniker range=">= aspnetcore-2.0"

Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování režimu Galois/Counter s ověřováním, vytvořte instanci [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) obsahující informace o algoritmech:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování režimu Galois/Counter s ověřováním, vytvořte instanci [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) obsahující informace o algoritmech:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> Algoritmus symetrického šifrování bloku musí mít délku klíče >= 128 bitů, velikost bloku je přesně 128 bitů a musí podporovat šifrování GCM. Vlastnost [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) můžete nastavit na hodnotu null, aby pro zadaný algoritmus používala výchozího zprostředkovatele. Další informace najdete v dokumentaci k [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

### <a name="specifying-other-custom-algorithms"></a>Určení dalších vlastních algoritmů

I když není zveřejněné jako rozhraní API první třídy, je systém ochrany dat dostatečně rozšiřitelný, aby bylo možné zadat skoro jakýkoli druh algoritmu. Například je možné uchovávat všechny klíče obsažené v modulu hardwarového zabezpečení (HSM) a poskytovat vlastní implementaci základních rutin šifrování a dešifrování. Další informace najdete v tématu [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) v [části rozšiřitelná kryptografie](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Trvalé ukládání klíčů při hostování v kontejneru Docker

Při hostování v kontejneru [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) by měly být klíče uchovávány v těchto umístěních:

* Složka, která je dokovacím svazkem, který přetrvává mimo dobu života kontejneru, jako je například sdílený svazek nebo svazek připojený k hostiteli.
* Externí poskytovatel, například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Trvalé ukládání klíčů pomocí Redis

Pro ukládání klíčů by měly být použity pouze verze Redis podporující [Trvalost dat Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) . [Úložiště objektů BLOB v Azure](/azure/storage/blobs/storage-blobs-introduction) je trvalé a dá se použít k ukládání klíčů. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
