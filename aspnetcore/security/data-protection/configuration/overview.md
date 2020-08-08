---
title: Konfigurace ochrany ASP.NET Core dat
author: rick-anderson
description: Přečtěte si, jak nakonfigurovat ochranu dat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 0ff211624b7cf363da393a627c761302d9f3d8ed
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019752"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="9fbfb-103">Konfigurace ochrany ASP.NET Core dat</span><span class="sxs-lookup"><span data-stu-id="9fbfb-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="9fbfb-104">Po inicializaci systému ochrany dat se použije [výchozí nastavení](xref:security/data-protection/configuration/default-settings) na základě operačního prostředí.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="9fbfb-105">Tato nastavení jsou obecně vhodná pro aplikace běžící na jednom počítači.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="9fbfb-106">V některých případech může vývojář chtít změnit výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="9fbfb-107">Aplikace je rozdělená mezi několik počítačů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="9fbfb-108">Z důvodu dodržování předpisů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-108">For compliance reasons.</span></span>

<span data-ttu-id="9fbfb-109">V těchto scénářích nabízí systém ochrany dat bohatá rozhraní API pro konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="9fbfb-110">Podobně jako u konfiguračních souborů by měl být datový Prstenový kanál ochrany dat chráněný pomocí příslušných oprávnění.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="9fbfb-111">Můžete zvolit šifrování klíčů v klidovém umístění, ale nebrání útočníkům v vytváření nových klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="9fbfb-112">V důsledku toho je zabezpečení vaší aplikace ovlivněno.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="9fbfb-113">Umístění úložiště nakonfigurované s ochranou dat by mělo mít přístup omezený jenom na samotnou aplikaci, podobně jako při ochraně konfiguračních souborů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="9fbfb-114">Pokud se například rozhodnete, že budete svůj klíč Ring ukládat na disk, použijte oprávnění systému souborů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="9fbfb-115">Zajistěte, aby byla ve vaší webové aplikaci při čtení, zápisu a vytváření přístupu k tomuto adresáři pouze identita, pod kterou vaše webová aplikace běží.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="9fbfb-116">Pokud používáte Azure Blob Storage, měla by mít možnost číst, zapisovat nebo vytvářet nové položky v úložišti objektů BLOB atd.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="9fbfb-117">Metoda rozšíření [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) vrátí [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="9fbfb-118">`IDataProtectionBuilder`Zpřístupňuje metody rozšíření, které lze řetězit ke konfiguraci možností ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9fbfb-119">Pro rozšíření ochrany dat používané v tomto článku jsou vyžadovány následující balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="9fbfb-120">Microsoft. AspNetCore. DataProtection. AzureStorage</span><span class="sxs-lookup"><span data-stu-id="9fbfb-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="9fbfb-121">Microsoft. AspNetCore. DataProtection. AzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="9fbfb-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="9fbfb-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="9fbfb-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="9fbfb-123">Pokud chcete ukládat klíče v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém pomocí [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="9fbfb-124">Nastavte umístění úložiště Key Ring (například [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="9fbfb-125">Umístění musí být nastavené, protože volání `ProtectKeysWithAzureKeyVault` implementuje rozhraní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , které zakazuje automatickou konfiguraci ochrany dat, včetně umístění úložiště s klíčovým kroužkem.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="9fbfb-126">Předchozí příklad používá službu Azure Blob Storage k uchování služby Key Ring.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="9fbfb-127">Další informace najdete v tématu [Zprostředkovatelé úložiště klíčů: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="9fbfb-128">Klíčového ringu můžete také zachovat místně pomocí [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="9fbfb-129">`keyIdentifier`Je identifikátor klíče trezoru klíčů, který se používá pro šifrování klíče.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="9fbfb-130">Například klíč vytvořený v trezoru klíčů s názvem `dataprotection` `contosokeyvault` obsahuje identifikátor klíče `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="9fbfb-131">Poskytněte aplikaci oprávnění k **rozbalení klíče** a **zabalení klíče** do trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="9fbfb-132">`ProtectKeysWithAzureKeyVault`přetížení</span><span class="sxs-lookup"><span data-stu-id="9fbfb-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="9fbfb-133">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) umožňuje použití [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) k tomu, aby systém ochrany dat mohl používat Trezor klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="9fbfb-134">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) umožňuje použití `ClientId` a [certifikátu x509](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) k tomu, aby systém ochrany dat mohl používat Trezor klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="9fbfb-135">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) povolí použití `ClientId` a `ClientSecret` k povolení systému ochrany dat pro použití trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="9fbfb-136">Pokud k ukládání a ochraně klíčů používáte kombinaci trezoru klíčů a úložiště Azure, `System.UriFormatException` bude vyvolána výjimka, pokud objekt blob, ve kterém klíče uložíte, ještě neexistuje.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-136">When using a combination of keyvault and azure storage to store and protect keys, a `System.UriFormatException` will be thrown if the blob to store the keys in does not already exist.</span></span> <span data-ttu-id="9fbfb-137">To lze ručně vytvořit před spuštěním aplikace nebo je `.ProtectKeysWithAzureKeyVault()` lze odebrat pro první spuštění pro vytvoření objektu BLOB a následně při jeho přidání do následujících spuštění.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-137">This can be manually created ahead of running the application, or `.ProtectKeysWithAzureKeyVault()` can be removed for the first run to create the blob in place, then adding it on for subsequent runs.</span></span> <span data-ttu-id="9fbfb-138">Doporučujeme `.ProtectKeysWithAzureKeyVault()` , abyste se ujistili, že je soubor vytvořený pomocí správného schématu a hodnot na místě.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-138">Removing `.ProtectKeysWithAzureKeyVault()` is advised, as this will ensure that the file is created with the proper schema and values in place.</span></span>

```csharp
var storageAccount = CloudStorageAccount.Parse("<storage account connection string">);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("<key store container name>");

var azureServiceTokenProvider = new AzureServiceTokenProvider();
var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
        azureServiceTokenProvider.KeyVaultTokenCallback));

services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage(container, "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(keyVaultClient, "<keyIdentifier>");
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="9fbfb-139">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="9fbfb-139">PersistKeysToFileSystem</span></span>

<span data-ttu-id="9fbfb-140">Chcete-li uložit klíče ve sdílené složce UNC místo ve výchozím umístění *% localappdata%* , nakonfigurujte systém pomocí [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="9fbfb-140">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="9fbfb-141">Pokud změníte umístění trvalosti klíčů, systém už nebude automaticky šifrovat klíče v klidovém režimu, protože neví, jestli je DPAPI vhodný šifrovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-141">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="9fbfb-142">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="9fbfb-142">ProtectKeysWith\*</span></span>

<span data-ttu-id="9fbfb-143">Systém můžete nakonfigurovat tak, aby chránil klíče v klidovém umístění voláním libovolného rozhraní API pro konfiguraci [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-143">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="9fbfb-144">Vezměte v úvahu následující příklad, který ukládá klíče do sdílené složky UNC a šifruje tyto klíče v klidovém formátu pomocí konkrétního certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-144">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9fbfb-145">V ASP.NET Core 2,1 nebo novějším můžete zadat [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), jako je například certifikát načtený ze souboru:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-145">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="9fbfb-146">Další příklady a diskuze o integrovaných mechanismech šifrování klíčů najdete v tématu [šifrování klíčů v klidovém umístění](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-146">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="9fbfb-147">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="9fbfb-147">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="9fbfb-148">V ASP.NET Core 2,1 nebo novějších můžete otáčet certifikáty a dešifrovací klíče v klidovém formátu pomocí pole [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certifikátů s [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="9fbfb-148">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="9fbfb-149">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="9fbfb-149">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="9fbfb-150">Pokud chcete systém nakonfigurovat tak, aby používal životnost klíče 14 dní namísto výchozích 90 dní, použijte [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="9fbfb-150">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="9fbfb-151">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="9fbfb-151">SetApplicationName</span></span>

<span data-ttu-id="9fbfb-152">Ve výchozím nastavení systém ochrany dat izoluje aplikace od sebe navzájem na základě jejich [kořenových cest obsahu](xref:fundamentals/index#content-root) , i když sdílí stejné fyzické úložiště klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-152">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="9fbfb-153">To aplikacím brání v porozumění chráněným datovým částem.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-153">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="9fbfb-154">Sdílení chráněných datových částí mezi aplikacemi:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-154">To share protected payloads among apps:</span></span>

* <span data-ttu-id="9fbfb-155">Nakonfigurujte <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> v každé aplikaci stejnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-155">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="9fbfb-156">Používejte stejnou verzi Data Protection API stacku napříč aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-156">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="9fbfb-157">V souborech projektu aplikace proveďte **jednu** z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-157">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="9fbfb-158">Odkázat na stejnou verzi sdíleného rozhraní prostřednictvím [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-158">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="9fbfb-159">Odkaz na stejnou verzi [balíčku ochrany dat](xref:security/data-protection/introduction#package-layout)</span><span class="sxs-lookup"><span data-stu-id="9fbfb-159">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="9fbfb-160">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="9fbfb-160">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="9fbfb-161">Můžete mít situaci, kdy nechcete, aby aplikace automaticky nastavila klíče (vytváření nových klíčů) při přístupu k vypršení platnosti.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-161">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="9fbfb-162">Příkladem může být aplikace nastavovaná v relaci primárního a sekundárního, kde pouze primární aplikace zodpovídá za důležité aspekty správy klíčů a sekundární aplikace má pouze pohled jen pro čtení ve službě Key Ring.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-162">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="9fbfb-163">Sekundární aplikace je možné nakonfigurovat tak, aby považovaly klíčový prstenec za jen pro čtení, a to konfigurací systému pomocí <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :</span><span class="sxs-lookup"><span data-stu-id="9fbfb-163">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="9fbfb-164">Izolace podle aplikace</span><span class="sxs-lookup"><span data-stu-id="9fbfb-164">Per-application isolation</span></span>

<span data-ttu-id="9fbfb-165">Když je systém ochrany dat poskytovaný ASP.NET Core hostitelem, automaticky izoluje aplikace od sebe navzájem, i když tyto aplikace běží pod stejným účtem pracovního procesu a používají stejný hlavní materiál klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-165">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="9fbfb-166">To je trochu podobné modifikátoru IsolateApps z prvku System. Web `<machineKey>` .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-166">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="9fbfb-167">Mechanismus izolace funguje tak, že zvažuje každou aplikaci v místním počítači jako jedinečného tenanta, takže <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> Kořenová složka pro všechny dané aplikace automaticky zahrnuje ID aplikace jako diskriminátor.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-167">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="9fbfb-168">Jedinečným ID aplikace je fyzická cesta aplikace:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-168">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="9fbfb-169">Pro aplikace hostované ve službě IIS je jedinečným ID fyzická cesta k aplikaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-169">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="9fbfb-170">Pokud je aplikace nasazená ve webové farmě, je tato hodnota stabilní za předpokladu, že prostředí služby IIS se konfigurují podobně napříč všemi počítači ve webové farmě.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-170">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="9fbfb-171">Pro samoobslužné aplikace běžící na [serveru Kestrel](xref:fundamentals/servers/index#kestrel)je jedinečné ID fyzickou cestou k aplikaci na disku.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-171">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="9fbfb-172">Jedinečný identifikátor je navržený tak, aby se předržel &mdash; jak jednotlivé aplikace, tak i samotný počítač.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-172">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="9fbfb-173">Tento mechanismus izolace předpokládá, že aplikace nejsou škodlivé.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-173">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="9fbfb-174">Škodlivá aplikace může vždycky ovlivnit jakoukoli jinou aplikaci spuštěnou v rámci stejného účtu pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-174">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="9fbfb-175">Ve sdíleném hostitelském prostředí, kde jsou aplikace vzájemně nedůvěryhodné, by měl poskytovatel hostingu podniknout kroky k zajištění izolace na úrovni operačního systému mezi aplikacemi, včetně oddělení základních úložišť klíčů aplikací.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-175">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="9fbfb-176">Pokud není systém ochrany dat poskytovaný ASP.NET Corem hostitelem (například při vytvoření instance prostřednictvím `DataProtectionProvider` konkrétního typu), je ve výchozím nastavení zakázána izolace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-176">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="9fbfb-177">Pokud je izolace aplikace zakázaná, můžou všechny aplikace, které používá stejný materiál klíče, sdílet datové části, pokud poskytují vhodné [účely](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-177">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="9fbfb-178">Pokud chcete v tomto prostředí poskytnout izolaci aplikace, zavolejte metodu [SetApplicationName](#setapplicationname) na objekt konfigurace a zadejte jedinečný název pro každou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-178">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="9fbfb-179">Změna algoritmů pomocí UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="9fbfb-179">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="9fbfb-180">Zásobník ochrany dat umožňuje změnit výchozí algoritmus používaný nově generovanými klíči.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-180">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="9fbfb-181">Nejjednodušší způsob, jak to provést, je volat [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) z zpětného volání konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-181">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="9fbfb-182">Výchozí EncryptionAlgorithm je AES-256-CBC a výchozí ValidationAlgorithm je HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-182">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="9fbfb-183">Výchozí zásady může nastavit správce systému pomocí [zásad pro celé počítače](xref:security/data-protection/configuration/machine-wide-policy), ale explicitní volání `UseCryptographicAlgorithms` přepisu výchozích zásad.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-183">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="9fbfb-184">Volání `UseCryptographicAlgorithms` umožňuje zadat požadovaný algoritmus z předdefinovaného předdefinovaného seznamu.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-184">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="9fbfb-185">Nemusíte si dělat starosti s implementací algoritmu.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-185">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="9fbfb-186">Ve výše uvedeném scénáři se systém ochrany dat při spuštění ve Windows pokusí použít implementaci CNG pro AES.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-186">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="9fbfb-187">V opačném případě se vrátí do spravované třídy [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-187">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="9fbfb-188">Implementaci můžete zadat ručně prostřednictvím volání [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-188">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="9fbfb-189">Změna algoritmů neovlivní existující klíče ve službě Key Ring.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-189">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="9fbfb-190">Ovlivňuje jenom nově vygenerované klíče.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-190">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="9fbfb-191">Určení vlastních spravovaných algoritmů</span><span class="sxs-lookup"><span data-stu-id="9fbfb-191">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="9fbfb-192">Chcete-li zadat vlastní spravované algoritmy, vytvořte instanci [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , která odkazuje na typy implementace:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-192">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="9fbfb-193">Chcete-li zadat vlastní spravované algoritmy, vytvořte instanci [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , která odkazuje na typy implementace:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-193">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="9fbfb-194">Obecně \* musí být vlastnosti typu odkazovány na konkrétní instantiable (prostřednictvím neveřejných konstruktorů konstruktoru bez parametrů [) SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) a [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), i když systémové speciální případy některé hodnoty jako `typeof(Aes)` pro pohodlí.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-194">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="9fbfb-195">SymmetricAlgorithm musí mít délku klíče s ≥ 128 bity a blokovou velikostí ≥ 64 bitů a musí podporovat šifrování v režimu CBC s odsazením PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-195">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="9fbfb-196">KeyedHashAlgorithm musí mít velikost Digest >= 128 bitů a musí podporovat klíče délky rovnající se délce Digest algoritmu hash.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-196">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="9fbfb-197">KeyedHashAlgorithm není striktně vyžadováno pro HMAC.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-197">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="9fbfb-198">Určení vlastních algoritmů služby Windows CNG</span><span class="sxs-lookup"><span data-stu-id="9fbfb-198">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="9fbfb-199">Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování v režimu CBC s ověřováním HMAC, vytvořte instanci [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) obsahující informace o algoritmech:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-199">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="9fbfb-200">Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování v režimu CBC s ověřováním HMAC, vytvořte instanci [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) obsahující informace o algoritmech:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-200">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="9fbfb-201">Algoritmus symetrického šifrování bloku musí mít délku klíče >= 128 bitů, velikost bloku >= 64 bitů a musí podporovat šifrování v CBC s odsazením PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="9fbfb-202">Algoritmus hash musí mít velikost algoritmu Digest >= 128 bitů a musí podporovat otevření pomocí \_ \_ \_ příznaku HMAC ALG popisovače symetrickými \_ .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-202">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="9fbfb-203">\*Vlastnosti zprostředkovatele lze nastavit na hodnotu null pro použití výchozího zprostředkovatele pro zadaný algoritmus.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-203">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="9fbfb-204">Další informace najdete v dokumentaci k [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-204">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="9fbfb-205">Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování režimu Galois/Counter s ověřováním, vytvořte instanci [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) obsahující informace o algoritmech:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-205">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="9fbfb-206">Pokud chcete zadat vlastní algoritmus Windows CNG pomocí šifrování režimu Galois/Counter s ověřováním, vytvořte instanci [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) obsahující informace o algoritmech:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-206">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="9fbfb-207">Algoritmus symetrického šifrování bloku musí mít délku klíče >= 128 bitů, velikost bloku je přesně 128 bitů a musí podporovat šifrování GCM.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-207">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="9fbfb-208">Vlastnost [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) můžete nastavit na hodnotu null, aby pro zadaný algoritmus používala výchozího zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-208">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="9fbfb-209">Další informace najdete v dokumentaci k [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="9fbfb-210">Určení dalších vlastních algoritmů</span><span class="sxs-lookup"><span data-stu-id="9fbfb-210">Specifying other custom algorithms</span></span>

<span data-ttu-id="9fbfb-211">I když není zveřejněné jako rozhraní API první třídy, je systém ochrany dat dostatečně rozšiřitelný, aby bylo možné zadat skoro jakýkoli druh algoritmu.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-211">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="9fbfb-212">Například je možné uchovávat všechny klíče obsažené v modulu hardwarového zabezpečení (HSM) a poskytovat vlastní implementaci základních rutin šifrování a dešifrování.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-212">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="9fbfb-213">Další informace najdete v tématu [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) v [části rozšiřitelná kryptografie](xref:security/data-protection/extensibility/core-crypto) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-213">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="9fbfb-214">Trvalé ukládání klíčů při hostování v kontejneru Docker</span><span class="sxs-lookup"><span data-stu-id="9fbfb-214">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="9fbfb-215">Při hostování v kontejneru [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) by měly být klíče uchovávány v těchto umístěních:</span><span class="sxs-lookup"><span data-stu-id="9fbfb-215">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="9fbfb-216">Složka, která je dokovacím svazkem, který přetrvává mimo dobu života kontejneru, jako je například sdílený svazek nebo svazek připojený k hostiteli.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-216">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="9fbfb-217">Externí poskytovatel, například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) nebo [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-217">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="9fbfb-218">Trvalé ukládání klíčů pomocí Redis</span><span class="sxs-lookup"><span data-stu-id="9fbfb-218">Persisting keys with Redis</span></span>

<span data-ttu-id="9fbfb-219">Pro ukládání klíčů by měly být použity pouze verze Redis podporující [Trvalost dat Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) .</span><span class="sxs-lookup"><span data-stu-id="9fbfb-219">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="9fbfb-220">[Úložiště objektů BLOB v Azure](/azure/storage/blobs/storage-blobs-introduction) je trvalé a dá se použít k ukládání klíčů.</span><span class="sxs-lookup"><span data-stu-id="9fbfb-220">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="9fbfb-221">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="9fbfb-221">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fbfb-222">Další materiály</span><span class="sxs-lookup"><span data-stu-id="9fbfb-222">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
