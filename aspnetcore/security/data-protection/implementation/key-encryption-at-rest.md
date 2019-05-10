---
title: Šifrování klíčů v klidovém stavu uložených v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ochrany dat ASP.NET Core klíče šifrování v klidovém stavu.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898773"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="1deb1-103">Šifrování klíčů v klidovém stavu uložených v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1deb1-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="1deb1-104">Systém ochrany dat [využívá mechanismus zjišťování ve výchozím nastavení](xref:security/data-protection/configuration/default-settings) k určení jak kryptografické klíče by měla být v klidovém stavu zašifrovaná.</span><span class="sxs-lookup"><span data-stu-id="1deb1-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="1deb1-105">Vývojář můžete přepsat mechanismus zjišťování a ručně zadat, jak by měl být klíčů v klidovém stavu zašifrovaná.</span><span class="sxs-lookup"><span data-stu-id="1deb1-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="1deb1-106">Pokud zadáte explicitní [klíče trvalého umístění](xref:security/data-protection/implementation/key-storage-providers), systém pro ochranu dat deregisters výchozí šifrování klíčů v mechanismu rest.</span><span class="sxs-lookup"><span data-stu-id="1deb1-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="1deb1-107">Klíče jsou v důsledku toho již šifrují při nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="1deb1-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="1deb1-108">Doporučujeme vám [zadejte mechanismus explicitní šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) pro nasazení v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="1deb1-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="1deb1-109">Šifrování v klidovém stavu mechanismus možnosti jsou popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="1deb1-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="1deb1-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1deb1-110">Azure Key Vault</span></span>

<span data-ttu-id="1deb1-111">Pro ukládání klíčů v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém s [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="1deb1-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="1deb1-112">Další informace najdete v tématu [Konfigurace ochrany dat ASP.NET Core: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="1deb1-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="1deb1-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="1deb1-113">Windows DPAPI</span></span>

<span data-ttu-id="1deb1-114">**Platí jenom pro nasazení Windows.**</span><span class="sxs-lookup"><span data-stu-id="1deb1-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="1deb1-115">Při použití rozhraní Windows DPAPI materiál klíče je zašifrovaný pomocí [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) před se ukládají do úložiště.</span><span class="sxs-lookup"><span data-stu-id="1deb1-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="1deb1-116">Rozhraní DPAPI je mechanismus příslušným šifrovacím pro data, která se vůbec nečte mimo aktuální počítač (i když je možné zálohovat tyto klíče do služby Active Directory, naleznete v tématu [DPAPI a cestovní profily](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="1deb1-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="1deb1-117">Pokud chcete nakonfigurovat šifrování DPAPI klíč v klidovém stavu, volání jednoho z [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) rozšiřující metody:</span><span class="sxs-lookup"><span data-stu-id="1deb1-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="1deb1-118">Pokud `ProtectKeysWithDpapi` je zavolán bez parametrů, pouze pro aktuální uživatelský účet Windows může dešifrovat trvalý kanál klíč.</span><span class="sxs-lookup"><span data-stu-id="1deb1-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="1deb1-119">Volitelně můžete zadat, že nějakému uživatelskému účtu počítače (nejen aktuální uživatelský účet) možné dešifrovat aktualizační kanál, který klíč:</span><span class="sxs-lookup"><span data-stu-id="1deb1-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="1deb1-120">Certifikát X.509</span><span class="sxs-lookup"><span data-stu-id="1deb1-120">X.509 certificate</span></span>

<span data-ttu-id="1deb1-121">Pokud aplikace je rozdělena mezi více počítačů, může být vhodné sdíleného certifikátu X.509 distribuovat napříč počítači a konfiguraci prostředí aplikace, které používají certifikát pro šifrování klíčů v klidovém stavu:</span><span class="sxs-lookup"><span data-stu-id="1deb1-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="1deb1-122">Z důvodu omezení rozhraní .NET Framework se podporují pouze certifikáty s CAPI privátních klíčů.</span><span class="sxs-lookup"><span data-stu-id="1deb1-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="1deb1-123">Podívejte se níže uvedený obsah možná řešení na těchto omezení.</span><span class="sxs-lookup"><span data-stu-id="1deb1-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="1deb1-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="1deb1-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="1deb1-125">**Tento mechanismus je k dispozici pouze v systému Windows 8 nebo Windows Server 2012 nebo novější.**</span><span class="sxs-lookup"><span data-stu-id="1deb1-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="1deb1-126">Od verze Windows 8, operační systém Windows podporuje rozhraní DPAPI-NG (také nazývané CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="1deb1-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="1deb1-127">Další informace najdete v tématu [o rozhraní DPAPI CNG](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="1deb1-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="1deb1-128">Objekt zabezpečení je zakódován jako pravidla popisovač ochrany.</span><span class="sxs-lookup"><span data-stu-id="1deb1-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="1deb1-129">V následujícím příkladu, který volá [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)pouze uživatel připojený k doméně se zadaným identifikátorem SID mohly dešifrovat aktualizační kanál, který klíč:</span><span class="sxs-lookup"><span data-stu-id="1deb1-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="1deb1-130">K dispozici je také na bez parametrů přetížení `ProtectKeysWithDpapiNG`.</span><span class="sxs-lookup"><span data-stu-id="1deb1-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="1deb1-131">Pomocí této metody usnadnění práce můžete zadat pravidlo "SID = {CURRENT_ACCOUNT_SID}", kde *CURRENT_ACCOUNT_SID* je SID aktuálního uživatelského účtu Windows:</span><span class="sxs-lookup"><span data-stu-id="1deb1-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="1deb1-132">V tomto scénáři je zodpovědný za distribuci šifrovací klíče použité operacemi DPAPI NG řadič domény AD.</span><span class="sxs-lookup"><span data-stu-id="1deb1-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="1deb1-133">Cílový uživatel může dešifrovat šifrovaná data z libovolného počítače připojeného k doméně (za předpokladu, že je proces spuštěn pod svou identitu).</span><span class="sxs-lookup"><span data-stu-id="1deb1-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="1deb1-134">Šifrování na základě certifikátů s Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="1deb1-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="1deb1-135">Pokud aplikace běží na Windows 8.1 nebo Windows Server 2012 R2 nebo novější, můžete použít Windows DPAPI-NG k provedení na základě certifikátů šifrování.</span><span class="sxs-lookup"><span data-stu-id="1deb1-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="1deb1-136">Použití řetězce popisovače pravidla "certifikát = HashId:THUMBPRINT", kde *kryptografický OTISK* je hexadecimální zakódovaná SHA1 kryptografický otisk certifikátu:</span><span class="sxs-lookup"><span data-stu-id="1deb1-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="1deb1-137">Libovolná aplikace nasměrovaného na tato úložiště musí běžet na Windows 8.1 nebo Windows Server 2012 R2 nebo novějším k dešifrování klíče.</span><span class="sxs-lookup"><span data-stu-id="1deb1-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="1deb1-138">Vlastní šifrovací klíče</span><span class="sxs-lookup"><span data-stu-id="1deb1-138">Custom key encryption</span></span>

<span data-ttu-id="1deb1-139">Pokud nejsou integrované mechanismy vhodné, Vývojář můžete zadat vlastní mechanismus šifrování klíče poskytnutím vlastní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="1deb1-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
