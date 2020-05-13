---
title: Šifrování klíčů v klidovém formátu v systému Windows a Azure pomocí ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core šifrování klíče ochrany dat v klidovém umístění.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: c927c926212aeb1263d15fd3fdc753c377b2e305
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153573"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="751c8-103">Šifrování klíčů v klidovém formátu v systému Windows a Azure pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="751c8-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="751c8-104">Systém ochrany dat [ve výchozím nastavení využívá mechanismus zjišťování](xref:security/data-protection/configuration/default-settings) , aby určil, jak by měly být šifrovací klíče zašifrované v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="751c8-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="751c8-105">Vývojář může přepsat mechanismus zjišťování a ručně zadat, jak by měly být klíče zašifrované v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="751c8-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="751c8-106">Pokud zadáte explicitní [umístění trvalosti klíčů](xref:security/data-protection/implementation/key-storage-providers), systém ochrany dat zruší registraci výchozího šifrování klíče v mechanismu REST.</span><span class="sxs-lookup"><span data-stu-id="751c8-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="751c8-107">V důsledku toho klíče už nejsou zašifrované v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="751c8-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="751c8-108">Pro produkční nasazení doporučujeme [zadat explicitní šifrovací mechanismus klíčů](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="751c8-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="751c8-109">V tomto tématu jsou popsány možnosti mechanismu šifrování v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="751c8-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="751c8-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="751c8-110">Azure Key Vault</span></span>

<span data-ttu-id="751c8-111">Pokud chcete ukládat klíče v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém pomocí [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="751c8-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="751c8-112">Další informace najdete v tématu [konfigurace ASP.NET Core ochrany dat: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="751c8-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="751c8-113">Rozhraní Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="751c8-113">Windows DPAPI</span></span>

<span data-ttu-id="751c8-114">**Platí jenom pro nasazení systému Windows.**</span><span class="sxs-lookup"><span data-stu-id="751c8-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="751c8-115">Je-li použit rozhraní DPAPI systému Windows, je klíčovým materiálem zašifrováno pomocí funkce [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) před trvalým uložením do úložiště.</span><span class="sxs-lookup"><span data-stu-id="751c8-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="751c8-116">Rozhraní DPAPI je vhodný šifrovací mechanismus pro data, která se nikdy nepřečetla mimo aktuální počítač (i když je možné tyto klíče do služby Active Directory obnovit), viz [profily DPAPI a cestovní profily](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="751c8-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="751c8-117">Pokud chcete nakonfigurovat šifrování v rámci neaktivního klíče DPAPI, zavolejte jednu z metod rozšíření [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="751c8-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="751c8-118">Pokud `ProtectKeysWithDpapi` je volána bez parametrů, lze trvale dekódovat trvalý klíčový prstenec pouze v aktuálním uživatelském účtu systému Windows.</span><span class="sxs-lookup"><span data-stu-id="751c8-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="751c8-119">Volitelně můžete určit, že každý uživatelský účet v počítači (nikoli jenom aktuální uživatelský účet) může dekódovat klíčového prstence:</span><span class="sxs-lookup"><span data-stu-id="751c8-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="751c8-120">Certifikát X. 509</span><span class="sxs-lookup"><span data-stu-id="751c8-120">X.509 certificate</span></span>

<span data-ttu-id="751c8-121">Pokud je aplikace rozdělená mezi několik počítačů, může být vhodné distribuovat sdílený certifikát X. 509 napříč počítači a nakonfigurovat hostované aplikace tak, aby používaly certifikát pro šifrování neaktivních klíčů:</span><span class="sxs-lookup"><span data-stu-id="751c8-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="751c8-122">Vzhledem k omezením .NET Framework podporují se jenom certifikáty s privátními klíči CAPI.</span><span class="sxs-lookup"><span data-stu-id="751c8-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="751c8-123">V níže uvedeném obsahu se můžete podívat na možná řešení těchto omezení.</span><span class="sxs-lookup"><span data-stu-id="751c8-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="751c8-124">Windows DPAPI – NG</span><span class="sxs-lookup"><span data-stu-id="751c8-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="751c8-125">**Tento mechanismus je k dispozici pouze v systému Windows 8/Windows Server 2012 nebo novějším.**</span><span class="sxs-lookup"><span data-stu-id="751c8-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="751c8-126">Počínaje systémem Windows 8 podporuje operační systém Windows DPAPI-NG (označuje se také jako CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="751c8-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="751c8-127">Další informace najdete v tématu [o CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="751c8-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="751c8-128">Objekt zabezpečení je kódovaný jako pravidlo popisovače ochrany.</span><span class="sxs-lookup"><span data-stu-id="751c8-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="751c8-129">V následujícím příkladu, který volá [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), může dešifrovat klíč Ring jenom uživatel připojený k doméně se zadaným identifikátorem SID:</span><span class="sxs-lookup"><span data-stu-id="751c8-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="751c8-130">Existuje také přetížení bez parametrů `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="751c8-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="751c8-131">Tuto metodu použijte k zadání pravidla "SID = {CURRENT_ACCOUNT_SID}", kde *CURRENT_ACCOUNT_SID* je identifikátor SID aktuálního uživatelského účtu systému Windows:</span><span class="sxs-lookup"><span data-stu-id="751c8-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="751c8-132">V tomto scénáři zodpovídá řadič domény služby AD za distribuci šifrovacích klíčů používaných operacemi DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="751c8-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="751c8-133">Cílový uživatel může dešifrovat šifrovanou datovou část z libovolného počítače připojeného k doméně (za předpokladu, že proces běží pod svou identitou).</span><span class="sxs-lookup"><span data-stu-id="751c8-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="751c8-134">Šifrování založené na certifikátech s Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="751c8-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="751c8-135">Pokud je aplikace spuštěná na Windows 8.1/Windows Serveru 2012 R2 nebo novějším, můžete k provádění šifrování založeného na certifikátech použít Windows DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="751c8-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="751c8-136">Použijte řetězec deskriptoru pravidel "CERTIFICATE = HashId: kryptografický otisk", kde *kryptografický otisk* je hex kódovaný otisk certifikátu SHA1:</span><span class="sxs-lookup"><span data-stu-id="751c8-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="751c8-137">Všechny aplikace, na které se odkazuje v tomto úložišti, musí běžet na Windows 8.1/Windows Serveru 2012 R2 nebo novějším, aby se klíče dešifroval.</span><span class="sxs-lookup"><span data-stu-id="751c8-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="751c8-138">Šifrování vlastních klíčů</span><span class="sxs-lookup"><span data-stu-id="751c8-138">Custom key encryption</span></span>

<span data-ttu-id="751c8-139">Pokud nejsou mechanismy v krabici vhodné, může vývojář zadat vlastní šifrovací mechanismus klíče tím, že poskytuje vlastní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="751c8-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
