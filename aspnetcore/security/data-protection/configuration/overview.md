---
title: Konfigurace ochrany dat ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nakonfigurovat ochranu dat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/08/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 36a06246513215ec29891df02688d113db11f914
ms.sourcegitcommit: 32bc00435767189fa3ae5fb8a91a307bf889de9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733491"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="e8cb4-103">Konfigurace ochrany dat ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8cb4-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="e8cb4-104">Při inicializaci systému ochrany dat, se vztahuje [výchozí nastavení](xref:security/data-protection/configuration/default-settings) podle právě takové provozní prostředí.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="e8cb4-105">Tato nastavení jsou obecně vhodné pro aplikace běžící na jednom počítači.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="e8cb4-106">Existují případy, ve kterém může vývojář chcete změnit výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="e8cb4-107">Aplikace se pak rozdělí mezi několik počítačů.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="e8cb4-108">Kvůli dodržování předpisů.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-108">For compliance reasons.</span></span>

<span data-ttu-id="e8cb4-109">Pro tyto scénáře ochrany dat systému nabízí bohaté konfigurační rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="e8cb4-110">Podobně jako u konfigurační soubory se aktualizační kanál, který data protection klíč by měly být chráněné pomocí příslušná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="e8cb4-111">Můžete také k šifrování klíčů v klidovém stavu, ale nezabrání útočníci vytvářet nové klíče.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="e8cb4-112">V důsledku toho je dopad na zabezpečení vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="e8cb4-113">Umístění úložiště s nakonfigurovanou ochranou dat měli přístup pouze pro samotné podobným způsobem, jakým byste chránit konfigurační soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="e8cb4-114">Například pokud se rozhodnete ukládat vaše klíče kanál na disk, pomocí oprávnění systému souborů.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="e8cb4-115">Zajištění pouze identitu, které běží vaše webová aplikace má ke čtení, zápisu a vytvoření přístup do tohoto adresáře.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="e8cb4-116">Pokud používáte Azure Table Storage, webové aplikace by měl mít možnost číst, zapsat nebo vytvořit nové položky v tabulce úložiště, atd.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-116">If you use Azure Table Storage, only the web app should have the ability to read, write, or create new entries in the table store, etc.</span></span>
>
> <span data-ttu-id="e8cb4-117">Metoda rozšíření [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) vrátí [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="e8cb4-118">`IDataProtectionBuilder` poskytuje rozšiřující metody, že můžete zřetězit dohromady konfigurace aplikace Data Protection možnosti.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="e8cb4-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="e8cb4-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="e8cb4-120">Pro ukládání klíčů v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém s [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="e8cb4-121">Nastavte umístění úložiště klíč prstenec (například [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="e8cb4-122">Umístění musí nastavit, protože volání `ProtectKeysWithAzureKeyVault` implementuje [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , která zakáže nastavení ochrany dat, včetně umístění úložiště klíč kanál.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="e8cb4-123">V předchozím příkladu používá úložiště objektů Blob v Azure k uchování aktualizační kanál, který klíč.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="e8cb4-124">Další informace najdete v tématu [zprostředkovateli úložiště klíčů: Azure a Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-124">For more information, see [Key storage providers: Azure and Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span></span> <span data-ttu-id="e8cb4-125">Můžete také zachovat aktualizační kanál, který klíč místně s [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="e8cb4-126">`keyIdentifier` Je identifikátor klíče služby key vault používá pro šifrování s klíčem (například `https://contosokeyvault.vault.azure.net/keys/dataprotection/`).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-126">The `keyIdentifier` is the key vault key identifier used for key encryption (for example, `https://contosokeyvault.vault.azure.net/keys/dataprotection/`).</span></span>

<span data-ttu-id="e8cb4-127">`ProtectKeysWithAzureKeyVault` přetížení:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-127">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="e8cb4-128">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) umožňuje použití [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) povolit systém ochrany dat pro použití služby key vault.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-128">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e8cb4-129">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) umožňuje použití `ClientId` a [certifikátu x 509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) povolit systém ochrany dat pro použití služby key vault.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-129">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e8cb4-130">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) umožňuje použití `ClientId` a `ClientSecret` povolit systém ochrany dat pro použití služby key vault.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="e8cb4-131">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="e8cb4-131">PersistKeysToFileSystem</span></span>

<span data-ttu-id="e8cb4-132">Pro ukládání klíčů na sdílenou jednotku UNC místo na *% LOCALAPPDATA %* výchozí umístění, konfigurace systému s [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="e8cb4-132">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="e8cb4-133">Pokud změníte umístění trvalost klíče, systém šifruje už nebude automaticky klíčů v klidovém stavu, protože nebude vědět, zda rozhraní DPAPI je příslušným šifrovacím mechanismem.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-133">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="e8cb4-134">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="e8cb4-134">ProtectKeysWith\*</span></span>

<span data-ttu-id="e8cb4-135">Můžete nakonfigurovat systému k ochraně klíčů v klidovém stavu pomocí některé z volání [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) rozhraní API pro konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-135">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="e8cb4-136">Vezměte v úvahu v příkladu níže, který ukládá klíče na sdílenou jednotku UNC a šifruje těchto klíčů v klidovém stavu pomocí konkrétního certifikátu X.509:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-136">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e8cb4-137">V ASP.NET Core 2.1 nebo novější, můžete zadat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) k [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), jako jsou certifikát načíst ze souboru:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-137">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="e8cb4-138">V tématu [klíč šifrování v Rest](xref:security/data-protection/implementation/key-encryption-at-rest) Další příklady a diskuse o mechanismy integrovaný šifrovací klíče.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-138">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="e8cb4-139">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="e8cb4-139">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="e8cb4-140">V ASP.NET Core 2.1 nebo novější, můžete Rotování certifikátů a klíčů v klidovém stavu pomocí pole dešifrovat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certifikáty [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="e8cb4-140">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="e8cb4-141">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="e8cb4-141">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="e8cb4-142">Chcete-li konfigurovat systém použít životnosti klíče 14 dnů místo výchozího 90 dnů, použijte [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="e8cb4-142">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="e8cb4-143">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="e8cb4-143">SetApplicationName</span></span>

<span data-ttu-id="e8cb4-144">Ve výchozím nastavení ochrana dat systému izoluje aplikace od sebe podle jejich obsahu kořenových cest, i v případě, že sdílíte stejný fyzický klíče úložiště.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-144">By default, the Data Protection system isolates apps from one another based on their content root paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="e8cb4-145">To zabrání Principy druhé strany chráněných datových částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-145">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="e8cb4-146">Sdílení chráněných datových částí mezi aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-146">To share protected payloads among apps:</span></span>

* <span data-ttu-id="e8cb4-147">Konfigurace <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> v každé aplikaci, se stejnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-147">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="e8cb4-148">Použijte stejnou verzi nástroje Data Protection API zásobníku napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-148">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="e8cb4-149">Provedení **buď** z následujících akcí v souborech projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-149">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="e8cb4-150">Odkazovat na stejnou verzi sdílené architektuře prostřednictvím [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-150">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="e8cb4-151">Odkazovat na stejný [ochranu dat balíčku](xref:security/data-protection/introduction#package-layout) verze.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-151">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="e8cb4-152">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="e8cb4-152">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="e8cb4-153">Můžete mít scénář, kde nechcete, aby aplikace pro automatickou změnu klíče (vytvářet nové klíče), protože přístup vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-153">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="e8cb4-154">Jedním z příkladů může být aplikace nastavit ve primárního a sekundárního relaci, kde jenom primární aplikace zodpovídá za správu klíčů připomínky a sekundární aplikace jednoduše mají přehled aktualizační kanál, který klíč jen pro čtení.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-154">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="e8cb4-155">Sekundární aplikace je možné nakonfigurovat přistupovat ke všem aktualizační kanál, který klíč jen pro čtení v systému s konfigurací [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span><span class="sxs-lookup"><span data-stu-id="e8cb4-155">The secondary apps can be configured to treat the key ring as read-only by configuring the system with [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="e8cb4-156">Izolace podle aplikací</span><span class="sxs-lookup"><span data-stu-id="e8cb4-156">Per-application isolation</span></span>

<span data-ttu-id="e8cb4-157">Když ochrana dat systému pochází od hostitele služby ASP.NET Core, je aplikace od sebe, automaticky izoluje, i v případě, že na aplikace, které jsou spuštěny pod stejným účtem pracovního procesu a používá stejný materiál hlavního klíče.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-157">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="e8cb4-158">Toto je poněkud podobně jako modifikátor IsolateApps z prostředí System.Web společnosti  **\<machineKey >** elementu.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-158">This is somewhat similar to the IsolateApps modifier from System.Web's **\<machineKey>** element.</span></span>

<span data-ttu-id="e8cb4-159">Mechanismus izolace funguje tak, že vzhledem k tomu každou aplikaci na místním počítači jako jedinečný tenanta, proto [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) root pro aplikace automaticky zahrnuje ID aplikace jako diskriminátor.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-159">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="e8cb4-160">Jedinečné ID aplikace pochází z jednoho z následujících dvou míst:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-160">The app's unique ID comes from one of two places:</span></span>

1. <span data-ttu-id="e8cb4-161">Pokud je aplikace hostovaná ve službě IIS, je jedinečný identifikátor aplikace konfigurační cesty.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-161">If the app is hosted in IIS, the unique identifier is the app's configuration path.</span></span> <span data-ttu-id="e8cb4-162">Pokud je aplikace nasazená v prostředí webové farmy, tato hodnota by měla být stabilní, za předpokladu, že služba IIS prostředí jsou nakonfigurované podobně jako ve všech počítačích ve webové farmě.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-162">If an app is deployed in a web farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>

2. <span data-ttu-id="e8cb4-163">Pokud aplikace není hostována ve službě IIS, je jedinečný identifikátor fyzická cesta aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-163">If the app isn't hosted in IIS, the unique identifier is the physical path of the app.</span></span>

<span data-ttu-id="e8cb4-164">Jedinečný identifikátor slouží k překonání resetování &mdash; jednotlivých aplikací a celý počítač.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-164">The unique identifier is designed to survive resets &mdash; both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="e8cb4-165">Tento mechanismus izolace předpokládá, že nejsou škodlivých aplikací.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-165">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="e8cb4-166">Škodlivé aplikace může ovlivnit vždy jakoukoli jinou aplikaci běžící pod stejný účet pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-166">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="e8cb4-167">Ve sdíleném hostování prostředí kdy jsou vzájemně nedůvěryhodných aplikace poskytovatel hostingu by měl zajistit OS úrovně izolace mezi aplikacemi, včetně oddělení aplikací základní klíče úložiště.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-167">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="e8cb4-168">Pokud ochranu dat systému není k dispozici od hostitele ASP.NET Core (např. Pokud ho přes instanci `DataProtectionProvider` konkrétní typ) je ve výchozím nastavení zakázaná izolace aplikací.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-168">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="e8cb4-169">Pokud je zakázaná izolace aplikací, všechny aplikace se zajištěnou stejný klíčový materiál můžete sdílet datových částí tak dlouho, dokud poskytují odpovídající [účely](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-169">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="e8cb4-170">K zajištění izolace aplikací v tomto prostředí, zavolejte [SetApplicationName](#setapplicationname) metoda při konfiguraci objektu a zadejte jedinečný název pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-170">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="e8cb4-171">Změna algoritmy UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="e8cb4-171">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="e8cb4-172">Ochrana dat zásobníku umožňuje změnit výchozí algoritmus používaný serverem nově vygenerovat klíče.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-172">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="e8cb4-173">Nejjednodušší způsob, jak to provést, je volání [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ze zpětného volání konfigurace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-173">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="e8cb4-174">Výchozí hodnota načtou algoritmy EncryptionAlgorithm je AES-256-CBC a ValidationAlgorithm výchozí hodnota je HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-174">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="e8cb4-175">Výchozí zásady můžete nastavit správce systému prostřednictvím [celého zásad](xref:security/data-protection/configuration/machine-wide-policy), ale explicitní volání konstruktoru `UseCryptographicAlgorithms` přepisuje výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-175">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="e8cb4-176">Volání `UseCryptographicAlgorithms` můžete zadat požadovaný algoritmus z předdefinovaného seznamu integrované.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-176">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="e8cb4-177">Nemusíte se starat o provádění algoritmu.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-177">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="e8cb4-178">Ve výše uvedeném scénáři ochranu dat systém se pokusí použít implementaci CNG AES, pokud běží na Windows.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-178">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="e8cb4-179">V opačném případě vrátí se zpět k managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) třídy.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-179">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="e8cb4-180">Můžete ručně zadat implementace prostřednictvím volání [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-180">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="e8cb4-181">Změna algoritmy neovlivní existující klíče v aktualizační kanál, který klíč.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-181">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="e8cb4-182">Ovlivňuje to jenom nově vygenerované klíče.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-182">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="e8cb4-183">Zadání vlastní spravované algoritmy</span><span class="sxs-lookup"><span data-stu-id="e8cb4-183">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8cb4-184">Pokud chcete zadat vlastní spravované algoritmů, vytvářet [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instanci, která odkazuje na implementaci typů:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-184">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e8cb4-185">Pokud chcete zadat vlastní spravované algoritmů, vytvářet [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instanci, která odkazuje na implementaci typů:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-185">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e8cb4-186">Obecně \*typ vlastnosti musí odkazovat na konkrétní, instantiable (přes veřejný konstruktor bez parametrů konstruktoru) implementace [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) a [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), i když systém speciální případech některé hodnoty, jako jsou `typeof(Aes)` ke zvýšení pohodlí.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-186">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="e8cb4-187">SymmetricAlgorithm musí mít klíč o délce ≥ 128 bitů a velikost bloku ≥ 64 bitů a musí podporovat režimu CBC šifrování s odsazení PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-187">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e8cb4-188">KeyedHashAlgorithm musí mít velikost digest > = 128 bitů a musí podporovat klíče o délce rovna délce hashovací algoritmus digest.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-188">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="e8cb4-189">KeyedHashAlgorithm není bezpodmínečně nutné, aby HMAC.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-189">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="e8cb4-190">Zadání vlastní algoritmů Windows CNG</span><span class="sxs-lookup"><span data-stu-id="e8cb4-190">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8cb4-191">Zadejte vlastní Windows CNG algoritmus HMAC ověření pomocí režimu CBC šifrování, vytvořit [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance, která obsahuje vylepšením informace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-191">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e8cb4-192">Zadejte vlastní Windows CNG algoritmus HMAC ověření pomocí režimu CBC šifrování, vytvořit [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance, která obsahuje vylepšením informace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-192">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e8cb4-193">Šifrovací algoritmus symetrického bloku musí mít klíč o délce > = 128 bitů, velikost bloku > = 64 bitů, a musí podporovat režimu CBC šifrování s odsazení PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-193">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e8cb4-194">Hashovací algoritmus musí mít velikost digest > = 128 bitů a musí podporovat otevírané pomocí BCRYPT\_ALG\_zpracování\_HMAC\_příznak příznak.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-194">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="e8cb4-195">\*Můžete nastavit vlastnosti zprostředkovatele hodnoty null lze použít výchozího poskytovatele pro zadaný algoritmus.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-195">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e8cb4-196">Zobrazit [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Další informace naleznete v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-196">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e8cb4-197">K určení vlastního algoritmu Windows CNG pomocí šifrování Galois/čítač režimu ověřování, vytvořit [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance, která obsahuje vylepšením informace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-197">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e8cb4-198">K určení vlastního algoritmu Windows CNG pomocí šifrování Galois/čítač režimu ověřování, vytvořit [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance, která obsahuje vylepšením informace:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-198">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e8cb4-199">Šifrovací algoritmus symetrického bloku musí mít klíč o délce > = 128 bitů, velikost bloku přesně 128 bitů, a musí podporovat šifrování služby GCM.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-199">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="e8cb4-200">Můžete nastavit [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) vlastnost na hodnotu null k použití výchozího zprostředkovatele pro zadaný algoritmus.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-200">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e8cb4-201">Zobrazit [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Další informace naleznete v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="e8cb4-202">Určení jiné vlastní algoritmy</span><span class="sxs-lookup"><span data-stu-id="e8cb4-202">Specifying other custom algorithms</span></span>

<span data-ttu-id="e8cb4-203">I když nejsou viditelné jako první třídy rozhraní API, ochrana dat systému je dostatečně rozšiřitelné, aby umožňoval zadání téměř jakýkoli druh algoritmus.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-203">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="e8cb4-204">Například je možné zachovat všechny klíče, které jsou obsaženy v rámci modulu hardwarového zabezpečení (HSM) a poskytuje vlastní implementace základní rutiny šifrování a dešifrování.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-204">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="e8cb4-205">Zobrazit [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) v [základní kryptografie rozšiřitelnost](xref:security/data-protection/extensibility/core-crypto) Další informace.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-205">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="e8cb4-206">Zachování klíče při hostování v kontejneru Dockeru</span><span class="sxs-lookup"><span data-stu-id="e8cb4-206">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="e8cb4-207">Při hostování v [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) kontejneru klíče by se měl zachovat buď:</span><span class="sxs-lookup"><span data-stu-id="e8cb4-207">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="e8cb4-208">Složka, která je svazek Dockeru, který potrvá déle než doba platnosti kontejneru, například sdíleného svazku nebo hostitele připojeného svazku.</span><span class="sxs-lookup"><span data-stu-id="e8cb4-208">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="e8cb4-209">Externího poskytovatele, jako například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="e8cb4-209">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8cb4-210">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e8cb4-210">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
