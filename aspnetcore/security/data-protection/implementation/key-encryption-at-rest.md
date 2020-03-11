---
title: Šifrování klíče v klidovém ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core šifrování klíče ochrany dat v klidovém umístění.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658387"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Šifrování klíče v klidovém ASP.NET Core

Systém ochrany dat [ve výchozím nastavení využívá mechanismus zjišťování](xref:security/data-protection/configuration/default-settings) , aby určil, jak by měly být šifrovací klíče zašifrované v klidovém stavu. Vývojář může přepsat mechanismus zjišťování a ručně zadat, jak by měly být klíče zašifrované v klidovém stavu.

> [!WARNING]
> Pokud zadáte explicitní [umístění trvalosti klíčů](xref:security/data-protection/implementation/key-storage-providers), systém ochrany dat zruší registraci výchozího šifrování klíče v mechanismu REST. V důsledku toho klíče už nejsou zašifrované v klidovém stavu. Pro produkční nasazení doporučujeme [zadat explicitní šifrovací mechanismus klíčů](xref:security/data-protection/implementation/key-encryption-at-rest) . V tomto tématu jsou popsány možnosti mechanismu šifrování v klidovém umístění.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Chcete-li uložit klíče v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), nakonfigurujte systém pomocí [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ve třídě `Startup`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Další informace najdete v tématu [konfigurace ASP.NET Core ochrany dat: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Platí jenom pro nasazení systému Windows.**

Je-li použit rozhraní DPAPI systému Windows, je klíčovým materiálem zašifrováno pomocí funkce [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) před trvalým uložením do úložiště. Rozhraní DPAPI je vhodný šifrovací mechanismus pro data, která se nikdy nepřečetla mimo aktuální počítač (i když je možné tyto klíče do služby Active Directory obnovit), viz [profily DPAPI a cestovní profily](https://support.microsoft.com/kb/309408/#6)). Pokud chcete nakonfigurovat šifrování v rámci neaktivního klíče DPAPI, zavolejte jednu z metod rozšíření [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Pokud je zavolána `ProtectKeysWithDpapi` bez parametrů, lze trvale dekódovat trvalý klíčový prstenec pouze v aktuálním uživatelském účtu systému Windows. Volitelně můžete určit, že každý uživatelský účet v počítači (nikoli jenom aktuální uživatelský účet) může dekódovat klíčového prstence:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certifikát X. 509

Pokud je aplikace rozdělená mezi několik počítačů, může být vhodné distribuovat sdílený certifikát X. 509 napříč počítači a nakonfigurovat hostované aplikace tak, aby používaly certifikát pro šifrování neaktivních klíčů:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Vzhledem k omezením .NET Framework podporují se jenom certifikáty s privátními klíči CAPI. V níže uvedeném obsahu se můžete podívat na možná řešení těchto omezení.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI – NG

**Tento mechanismus je k dispozici pouze v systému Windows 8/Windows Server 2012 nebo novějším.**

Počínaje systémem Windows 8 podporuje operační systém Windows DPAPI-NG (označuje se také jako CNG DPAPI). Další informace najdete v tématu [o CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Objekt zabezpečení je kódovaný jako pravidlo popisovače ochrany. V následujícím příkladu, který volá [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), může dešifrovat klíč Ring jenom uživatel připojený k doméně se zadaným identifikátorem SID:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

K dispozici je také přetížení `ProtectKeysWithDpapiNG`bez parametrů. Tuto metodu použijte k zadání pravidla "SID = {CURRENT_ACCOUNT_SID}", kde *CURRENT_ACCOUNT_SID* je identifikátor SID aktuálního uživatelského účtu systému Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

V tomto scénáři zodpovídá řadič domény služby AD za distribuci šifrovacích klíčů používaných operacemi DPAPI-NG. Cílový uživatel může dešifrovat šifrovanou datovou část z libovolného počítače připojeného k doméně (za předpokladu, že proces běží pod svou identitou).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Šifrování založené na certifikátech s Windows DPAPI-NG

Pokud je aplikace spuštěná na Windows 8.1/Windows Serveru 2012 R2 nebo novějším, můžete k provádění šifrování založeného na certifikátech použít Windows DPAPI-NG. Použijte řetězec deskriptoru pravidel "CERTIFICATE = HashId: kryptografický otisk", kde *kryptografický otisk* je hex kódovaný otisk certifikátu SHA1:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Všechny aplikace, na které se odkazuje v tomto úložišti, musí běžet na Windows 8.1/Windows Serveru 2012 R2 nebo novějším, aby se klíče dešifroval.

## <a name="custom-key-encryption"></a>Šifrování vlastních klíčů

Pokud nejsou mechanismy v krabici vhodné, může vývojář zadat vlastní šifrovací mechanismus klíče tím, že poskytuje vlastní [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
