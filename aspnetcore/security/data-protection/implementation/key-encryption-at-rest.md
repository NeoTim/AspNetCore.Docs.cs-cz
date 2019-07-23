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
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Šifrování klíčů v klidovém stavu uložených v ASP.NET Core

Systém ochrany dat [využívá mechanismus zjišťování ve výchozím nastavení](xref:security/data-protection/configuration/default-settings) k určení jak kryptografické klíče by měla být v klidovém stavu zašifrovaná. Vývojář můžete přepsat mechanismus zjišťování a ručně zadat, jak by měl být klíčů v klidovém stavu zašifrovaná.

> [!WARNING]
> Pokud zadáte explicitní [klíče trvalého umístění](xref:security/data-protection/implementation/key-storage-providers), systém pro ochranu dat deregisters výchozí šifrování klíčů v mechanismu rest. Klíče jsou v důsledku toho již šifrují při nečinnosti. Doporučujeme vám [zadejte mechanismus explicitní šifrování klíče](xref:security/data-protection/implementation/key-encryption-at-rest) pro nasazení v produkčním prostředí. Šifrování v klidovém stavu mechanismus možnosti jsou popsané v tomto tématu.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Pro ukládání klíčů v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém s [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) v `Startup` třídy:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Další informace najdete v tématu [Konfigurace ochrany dat ASP.NET Core: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Platí jenom pro nasazení Windows.**

Při použití rozhraní Windows DPAPI materiál klíče je zašifrovaný pomocí [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) před se ukládají do úložiště. Rozhraní DPAPI je mechanismus příslušným šifrovacím pro data, která se vůbec nečte mimo aktuální počítač (i když je možné zálohovat tyto klíče do služby Active Directory, naleznete v tématu [DPAPI a cestovní profily](https://support.microsoft.com/kb/309408/#6)). Pokud chcete nakonfigurovat šifrování DPAPI klíč v klidovém stavu, volání jednoho z [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) rozšiřující metody:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Pokud `ProtectKeysWithDpapi` je zavolán bez parametrů, pouze pro aktuální uživatelský účet Windows může dešifrovat trvalý kanál klíč. Volitelně můžete zadat, že nějakému uživatelskému účtu počítače (nejen aktuální uživatelský účet) možné dešifrovat aktualizační kanál, který klíč:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certifikát X.509

Pokud aplikace je rozdělena mezi více počítačů, může být vhodné sdíleného certifikátu X.509 distribuovat napříč počítači a konfiguraci prostředí aplikace, které používají certifikát pro šifrování klíčů v klidovém stavu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Z důvodu omezení rozhraní .NET Framework se podporují pouze certifikáty s CAPI privátních klíčů. Podívejte se níže uvedený obsah možná řešení na těchto omezení.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Tento mechanismus je k dispozici pouze v systému Windows 8 nebo Windows Server 2012 nebo novější.**

Od verze Windows 8, operační systém Windows podporuje rozhraní DPAPI-NG (také nazývané CNG DPAPI). Další informace najdete v tématu [o rozhraní DPAPI CNG](/windows/desktop/SecCNG/cng-dpapi).

Objekt zabezpečení je zakódován jako pravidla popisovač ochrany. V následujícím příkladu, který volá [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)pouze uživatel připojený k doméně se zadaným identifikátorem SID mohly dešifrovat aktualizační kanál, který klíč:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

K dispozici je také na bez parametrů přetížení `ProtectKeysWithDpapiNG`. Pomocí této metody usnadnění práce můžete zadat pravidlo "SID = {CURRENT_ACCOUNT_SID}", kde *CURRENT_ACCOUNT_SID* je SID aktuálního uživatelského účtu Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

V tomto scénáři je zodpovědný za distribuci šifrovací klíče použité operacemi DPAPI NG řadič domény AD. Cílový uživatel může dešifrovat šifrovaná data z libovolného počítače připojeného k doméně (za předpokladu, že je proces spuštěn pod svou identitu).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Šifrování na základě certifikátů s Windows DPAPI-NG

Pokud aplikace běží na Windows 8.1 nebo Windows Server 2012 R2 nebo novější, můžete použít Windows DPAPI-NG k provedení na základě certifikátů šifrování. Použití řetězce popisovače pravidla "certifikát = HashId:THUMBPRINT", kde *kryptografický OTISK* je hexadecimální zakódovaná SHA1 kryptografický otisk certifikátu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Libovolná aplikace nasměrovaného na tato úložiště musí běžet na Windows 8.1 nebo Windows Server 2012 R2 nebo novějším k dešifrování klíče.

## <a name="custom-key-encryption"></a>Vlastní šifrovací klíče

Pokud nejsou integrované mechanismy vhodné, Vývojář můžete zadat vlastní mechanismus šifrování klíče poskytnutím vlastní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
