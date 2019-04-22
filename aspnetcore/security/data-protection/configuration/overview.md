---
title: Konfigurace ochrany dat ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nakonfigurovat ochranu dat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: ee43427fa1e82a365d49df50567b4ca7afb5a5d3
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516245"
---
# <a name="configure-aspnet-core-data-protection"></a>Konfigurace ochrany dat ASP.NET Core

Při inicializaci systému ochrany dat, se vztahuje [výchozí nastavení](xref:security/data-protection/configuration/default-settings) podle právě takové provozní prostředí. Tato nastavení jsou obecně vhodné pro aplikace běžící na jednom počítači. Existují případy, ve kterém může vývojář chcete změnit výchozí nastavení:

* Aplikace se pak rozdělí mezi několik počítačů.
* Kvůli dodržování předpisů.

Pro tyto scénáře ochrany dat systému nabízí bohaté konfigurační rozhraní API.

> [!WARNING]
> Podobně jako u konfigurační soubory se aktualizační kanál, který data protection klíč by měly být chráněné pomocí příslušná oprávnění. Můžete také k šifrování klíčů v klidovém stavu, ale nezabrání útočníci vytvářet nové klíče. V důsledku toho je dopad na zabezpečení vaší aplikace. Umístění úložiště s nakonfigurovanou ochranou dat měli přístup pouze pro samotné podobným způsobem, jakým byste chránit konfigurační soubory aplikace. Například pokud se rozhodnete ukládat vaše klíče kanál na disk, pomocí oprávnění systému souborů. Zajištění pouze identitu, které běží vaše webová aplikace má ke čtení, zápisu a vytvoření přístup do tohoto adresáře. Pokud používáte Azure Table Storage, webové aplikace by měl mít možnost číst, zapsat nebo vytvořit nové položky v tabulce úložiště, atd.
>
> Metoda rozšíření [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) vrátí [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder` poskytuje rozšiřující metody, že můžete zřetězit dohromady konfigurace aplikace Data Protection možnosti.

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Pro ukládání klíčů v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém s [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) v `Startup` třídy:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Nastavte umístění úložiště klíč prstenec (například [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Umístění musí nastavit, protože volání `ProtectKeysWithAzureKeyVault` implementuje [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , která zakáže nastavení ochrany dat, včetně umístění úložiště klíč kanál. V předchozím příkladu používá úložiště objektů Blob v Azure k uchování aktualizační kanál, který klíč. Další informace najdete v tématu [zprostředkovateli úložiště klíčů: Azure a Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis). Můžete také zachovat aktualizační kanál, který klíč místně s [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier` Je identifikátor klíče služby key vault používá pro šifrování s klíčem. Například klíče vytvořeného ve službě key vault s názvem `dataprotection` v `contosokeyvault` má identifikátor klíče `https://contosokeyvault.vault.azure.net/keys/dataprotection/`. Poskytují aplikace s **rozbalení klíče** a **zabalit klíč** oprávnění k trezoru klíčů.

`ProtectKeysWithAzureKeyVault` přetížení:

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) umožňuje použití [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) povolit systém ochrany dat pro použití služby key vault.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) umožňuje použití `ClientId` a [certifikátu x 509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) povolit systém ochrany dat pro použití služby key vault.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) umožňuje použití `ClientId` a `ClientSecret` povolit systém ochrany dat pro použití služby key vault.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Pro ukládání klíčů na sdílenou jednotku UNC místo na *% LOCALAPPDATA %* výchozí umístění, konfigurace systému s [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Pokud změníte umístění trvalost klíče, systém šifruje už nebude automaticky klíčů v klidovém stavu, protože nebude vědět, zda rozhraní DPAPI je příslušným šifrovacím mechanismem.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Můžete nakonfigurovat systému k ochraně klíčů v klidovém stavu pomocí některé z volání [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) rozhraní API pro konfiguraci. Vezměte v úvahu v příkladu níže, který ukládá klíče na sdílenou jednotku UNC a šifruje těchto klíčů v klidovém stavu pomocí konkrétního certifikátu X.509:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

V ASP.NET Core 2.1 nebo novější, můžete zadat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) k [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), jako jsou certifikát načíst ze souboru:

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

V tématu [klíč šifrování v Rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další příklady a diskuse o mechanismy integrovaný šifrovací klíče.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

V ASP.NET Core 2.1 nebo novější, můžete Rotování certifikátů a klíčů v klidovém stavu pomocí pole dešifrovat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certifikáty [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

Chcete-li konfigurovat systém použít životnosti klíče 14 dnů místo výchozího 90 dnů, použijte [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Ve výchozím nastavení ochrana dat systému izoluje aplikace od sebe podle jejich obsahu kořenových cest, i v případě, že sdílíte stejný fyzický klíče úložiště. To zabrání Principy druhé strany chráněných datových částí aplikace.

Sdílení chráněných datových částí mezi aplikacemi:

* Konfigurace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> v každé aplikaci, se stejnou hodnotou.
* Použijte stejnou verzi nástroje Data Protection API zásobníku napříč aplikací. Provedení **buď** z následujících akcí v souborech projektu aplikace:
  * Odkazovat na stejnou verzi sdílené architektuře prostřednictvím [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).
  * Odkazovat na stejný [ochranu dat balíčku](xref:security/data-protection/introduction#package-layout) verze.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Můžete mít scénář, kde nechcete, aby aplikace pro automatickou změnu klíče (vytvářet nové klíče), protože přístup vypršení platnosti. Jedním z příkladů může být aplikace nastavit ve primárního a sekundárního relaci, kde jenom primární aplikace zodpovídá za správu klíčů připomínky a sekundární aplikace jednoduše mají přehled aktualizační kanál, který klíč jen pro čtení. Sekundární aplikace je možné nakonfigurovat přistupovat ke všem aktualizační kanál, který klíč jen pro čtení v systému s konfigurací <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Izolace podle aplikací

Když ochrana dat systému pochází od hostitele služby ASP.NET Core, je aplikace od sebe, automaticky izoluje, i v případě, že na aplikace, které jsou spuštěny pod stejným účtem pracovního procesu a používá stejný materiál hlavního klíče. Toto je poněkud podobně jako modifikátor IsolateApps z prostředí System.Web společnosti `<machineKey>` elementu.

Mechanismus izolace funguje tak, že vzhledem k tomu každou aplikaci na místním počítači jako jedinečný tenanta, proto <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> root pro aplikace automaticky zahrnuje ID aplikace jako diskriminátor. Jedinečné ID aplikace je fyzická cesta aplikace:

* Pro aplikace hostované v [IIS](xref:fundamentals/servers/index#iis-http-server), jedinečné ID je fyzická cesta služby IIS aplikace. Pokud je aplikace nasazená v prostředí webové farmy, tato hodnota je stabilní, za předpokladu, že služba IIS prostředí jsou nakonfigurované podobně jako ve všech počítačích ve webové farmě.
* V místním prostředí aplikací a systémem [Kestrel server](xref:fundamentals/servers/index#kestrel), jedinečné ID je fyzická cesta k aplikaci na disku.

Jedinečný identifikátor slouží k překonání resetování&mdash;jednotlivých aplikací a celý počítač.

Tento mechanismus izolace předpokládá, že nejsou škodlivých aplikací. Škodlivé aplikace může ovlivnit vždy jakoukoli jinou aplikaci běžící pod stejný účet pracovního procesu. Ve sdíleném hostování prostředí kdy jsou vzájemně nedůvěryhodných aplikace poskytovatel hostingu by měl zajistit OS úrovně izolace mezi aplikacemi, včetně oddělení aplikací základní klíče úložiště.

Pokud ochranu dat systému není k dispozici od hostitele ASP.NET Core (např. Pokud ho přes instanci `DataProtectionProvider` konkrétní typ) je ve výchozím nastavení zakázaná izolace aplikací. Pokud je zakázaná izolace aplikací, všechny aplikace se zajištěnou stejný klíčový materiál můžete sdílet datových částí tak dlouho, dokud poskytují odpovídající [účely](xref:security/data-protection/consumer-apis/purpose-strings). K zajištění izolace aplikací v tomto prostředí, zavolejte [SetApplicationName](#setapplicationname) metoda při konfiguraci objektu a zadejte jedinečný název pro každou aplikaci.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Změna algoritmy UseCryptographicAlgorithms

Ochrana dat zásobníku umožňuje změnit výchozí algoritmus používaný serverem nově vygenerovat klíče. Nejjednodušší způsob, jak to provést, je volání [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ze zpětného volání konfigurace:

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

Výchozí hodnota načtou algoritmy EncryptionAlgorithm je AES-256-CBC a ValidationAlgorithm výchozí hodnota je HMACSHA256. Výchozí zásady můžete nastavit správce systému prostřednictvím [celého zásad](xref:security/data-protection/configuration/machine-wide-policy), ale explicitní volání konstruktoru `UseCryptographicAlgorithms` přepisuje výchozí zásady.

Volání `UseCryptographicAlgorithms` můžete zadat požadovaný algoritmus z předdefinovaného seznamu integrované. Nemusíte se starat o provádění algoritmu. Ve výše uvedeném scénáři ochranu dat systém se pokusí použít implementaci CNG AES, pokud běží na Windows. V opačném případě vrátí se zpět k managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) třídy.

Můžete ručně zadat implementace prostřednictvím volání [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Změna algoritmy neovlivní existující klíče v aktualizační kanál, který klíč. Ovlivňuje to jenom nově vygenerované klíče.

### <a name="specifying-custom-managed-algorithms"></a>Zadání vlastní spravované algoritmy

::: moniker range=">= aspnetcore-2.0"

Pokud chcete zadat vlastní spravované algoritmů, vytvářet [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instanci, která odkazuje na implementaci typů:

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

Pokud chcete zadat vlastní spravované algoritmů, vytvářet [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instanci, která odkazuje na implementaci typů:

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

Obecně \*typ vlastnosti musí odkazovat na konkrétní, instantiable (přes veřejný konstruktor bez parametrů konstruktoru) implementace [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) a [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), i když systém speciální případech některé hodnoty, jako jsou `typeof(Aes)` ke zvýšení pohodlí.

> [!NOTE]
> SymmetricAlgorithm musí mít klíč o délce ≥ 128 bitů a velikost bloku ≥ 64 bitů a musí podporovat režimu CBC šifrování s odsazení PKCS #7. KeyedHashAlgorithm musí mít velikost digest > = 128 bitů a musí podporovat klíče o délce rovna délce hashovací algoritmus digest. KeyedHashAlgorithm není bezpodmínečně nutné, aby HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Zadání vlastní algoritmů Windows CNG

::: moniker range=">= aspnetcore-2.0"

Zadejte vlastní Windows CNG algoritmus HMAC ověření pomocí režimu CBC šifrování, vytvořit [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance, která obsahuje vylepšením informace:

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

Zadejte vlastní Windows CNG algoritmus HMAC ověření pomocí režimu CBC šifrování, vytvořit [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance, která obsahuje vylepšením informace:

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
> Šifrovací algoritmus symetrického bloku musí mít klíč o délce > = 128 bitů, velikost bloku > = 64 bitů, a musí podporovat režimu CBC šifrování s odsazení PKCS #7. Hashovací algoritmus musí mít velikost digest > = 128 bitů a musí podporovat otevírané pomocí BCRYPT\_ALG\_zpracování\_HMAC\_příznak příznak. \*Můžete nastavit vlastnosti zprostředkovatele hodnoty null lze použít výchozího poskytovatele pro zadaný algoritmus. Zobrazit [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Další informace naleznete v dokumentaci.

::: moniker range=">= aspnetcore-2.0"

K určení vlastního algoritmu Windows CNG pomocí šifrování Galois/čítač režimu ověřování, vytvořit [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance, která obsahuje vylepšením informace:

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

K určení vlastního algoritmu Windows CNG pomocí šifrování Galois/čítač režimu ověřování, vytvořit [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance, která obsahuje vylepšením informace:

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
> Šifrovací algoritmus symetrického bloku musí mít klíč o délce > = 128 bitů, velikost bloku přesně 128 bitů, a musí podporovat šifrování služby GCM. Můžete nastavit [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) vlastnost na hodnotu null k použití výchozího zprostředkovatele pro zadaný algoritmus. Zobrazit [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Další informace naleznete v dokumentaci.

### <a name="specifying-other-custom-algorithms"></a>Určení jiné vlastní algoritmy

I když nejsou viditelné jako první třídy rozhraní API, ochrana dat systému je dostatečně rozšiřitelné, aby umožňoval zadání téměř jakýkoli druh algoritmus. Například je možné zachovat všechny klíče, které jsou obsaženy v rámci modulu hardwarového zabezpečení (HSM) a poskytuje vlastní implementace základní rutiny šifrování a dešifrování. Zobrazit [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) v [základní kryptografie rozšiřitelnost](xref:security/data-protection/extensibility/core-crypto) Další informace.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Zachování klíče při hostování v kontejneru Dockeru

Při hostování v [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) kontejneru klíče by se měl zachovat buď:

* Složka, která je svazek Dockeru, který potrvá déle než doba platnosti kontejneru, například sdíleného svazku nebo hostitele připojeného svazku.
* Externího poskytovatele, jako například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
