---
title: Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat aplikaci pomocí dvojice název hodnota v době běhu načteny pomocí zprostředkovatele konfigurace trezoru klíčů Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 78c63cf135ca92f0b5f6c6828b2ae34a44a7b36c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621013"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="ecef5-103">Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecef5-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="ecef5-104">Podle [Luke Latham](https://github.com/guardrex) a [Andrew Stanton sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ecef5-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="ecef5-105">Tento dokument popisuje, jak používat [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytovatel konfigurace pro načtení hodnoty konfigurace aplikace z Azure Key Vault tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="ecef5-106">Azure Key Vault je Cloudová služba, která pomáhá zajistit ochranu kryptografických klíčů a tajných kódů používaných aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="ecef5-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="ecef5-107">Časté scénáře pro používání služby Azure Key Vault s aplikací ASP.NET Core patří:</span><span class="sxs-lookup"><span data-stu-id="ecef5-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="ecef5-108">Řízení přístupu k citlivým konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="ecef5-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="ecef5-109">Splnění požadavků pro FIPS 140-2 úrovně 2 ověřit modulů hardwarového zabezpečení (HSM) při ukládání konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="ecef5-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="ecef5-110">Tento scénář je k dispozici pro aplikace, které cílí ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ecef5-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="ecef5-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ecef5-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="ecef5-112">Balíčky</span><span class="sxs-lookup"><span data-stu-id="ecef5-112">Packages</span></span>

<span data-ttu-id="ecef5-113">Použití zprostředkovatele konfigurace trezoru klíčů Azure, přidejte odkaz na balíček [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="ecef5-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="ecef5-114">Přijmout [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) scénář, přidejte odkaz na balíček [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="ecef5-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="ecef5-115">V době psaní, nejnovější stabilní verzi `Microsoft.Azure.Services.AppAuthentication`, verze `1.0.3`, poskytuje podporu pro [systém přiřadil spravovaných identit](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="ecef5-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="ecef5-116">Podpora pro *uživatelsky přiřazené identity spravované* je k dispozici v `1.2.0-preview2` balíčku.</span><span class="sxs-lookup"><span data-stu-id="ecef5-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="ecef5-117">Toto téma popisuje použití identit spravovaných systému a připravená ukázková aplikace používá verzi `1.0.3` z `Microsoft.Azure.Services.AppAuthentication` balíčku.</span><span class="sxs-lookup"><span data-stu-id="ecef5-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="ecef5-118">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="ecef5-118">Sample app</span></span>

<span data-ttu-id="ecef5-119">Ukázková aplikace spouští v jednom ze dvou režimů, které jsou určeny `#define` příkazu v horní části *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="ecef5-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="ecef5-120">`Certificate` &ndash; Ukazuje použití certifikát ID klienta aplikace Azure Key Vault a X.509 pro přístup k tajnými kódy uloženými v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ecef5-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="ecef5-121">Tuto verzi vzorku můžete spustit z libovolného místa a nasadit do Azure App Service nebo libovolného hostitele, která je schopná obsluhovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ecef5-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="ecef5-122">`Managed` &ndash; Ukazuje, jak používat [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do služby Azure Key Vault pomocí ověřování Azure AD bez přihlašovací údaje uložené v kódu nebo konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="ecef5-123">Při použití spravované identity k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) se nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="ecef5-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="ecef5-124">`Managed` Verzi vzorku se musí nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="ecef5-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="ecef5-125">Postupujte podle pokynů v [pomocí spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) oddílu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="ecef5-126">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktivy preprocesoru (`#define`), najdete v článku <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="ecef5-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="ecef5-127">Úložiště tajných kódů ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="ecef5-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="ecef5-128">Nastavení tajných kódů pomocí místně [nástroj tajný klíč správce](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ecef5-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ecef5-129">Spuštění ukázkové aplikace v místním počítači, ve vývojovém prostředí jsou načteny tajné klíče z místního úložiště tajný klíč správce.</span><span class="sxs-lookup"><span data-stu-id="ecef5-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="ecef5-130">Vyžaduje nástroj tajný klíč správce `<UserSecretsId>` vlastnost v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="ecef5-131">Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="ecef5-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="ecef5-132">Tajné kódy jsou vytvořeny jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="ecef5-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="ecef5-133">Použít hierarchická hodnoty (konfigurační oddíly funkce) `:` (dvojtečka) jako oddělovač v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index) názvů klíčů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="ecef5-134">Tajný klíč správce se používá z příkazové okno otevřít obsahu kořen projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="ecef5-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="ecef5-135">Spusťte následující příkazy v příkazovém řádku z obsahu kořenového adresáře projektu k nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="ecef5-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="ecef5-136">Když tyto tajné kódy jsou uložené ve službě Azure Key Vault v [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) části `_dev` přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="ecef5-137">Přípona poskytuje vizuální upozornění ve výstupu aplikace označující zdrojové hodnoty konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="ecef5-138">Úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ecef5-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="ecef5-139">Podle pokynů uvedených [rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI](/azure/key-vault/quick-create-cli) tématu této části jsou shrnuty pro vytvoření služby Azure Key Vault a uložení tajných kódů používaných ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ecef5-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="ecef5-140">Viz téma pro další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="ecef5-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="ecef5-141">Otevřete Azure Cloud shell pomocí některého z následujících metod v [webu Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="ecef5-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="ecef5-142">Vyberte **vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="ecef5-143">Použijte hledaný řetězec "Azure CLI" v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="ecef5-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="ecef5-144">Otevřete Cloud Shell v prohlížeči s **spustit Cloud Shell** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ecef5-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="ecef5-145">Vyberte **Cloud Shell** tlačítko v pravém horním rohu webu Azure portal v nabídce.</span><span class="sxs-lookup"><span data-stu-id="ecef5-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="ecef5-146">Další informace najdete v tématu [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) a [Přehled služby Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="ecef5-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="ecef5-147">Pokud již nejste ověřeni, přihlaste se `az login` příkazu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="ecef5-148">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro nové skupiny prostředků a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="ecef5-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ecef5-149">Vytvoření trezoru klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název pro nový trezor klíčů a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="ecef5-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ecef5-150">Vytvoření tajných kódů v trezoru klíčů jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="ecef5-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="ecef5-151">Azure Key Vault tajný názvy jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="ecef5-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="ecef5-152">Hierarchické hodnoty (konfigurační oddíly) použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="ecef5-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ecef5-153">Použití dvojteček, které se obvykle používají pro vymezení část z podklíč v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v názvech tajného kódu trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="ecef5-154">Proto jsou použít dvě pomlčky a má Prohodit pro dvojtečkou tajné klíče jsou načtena do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="ecef5-155">Tyto tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="ecef5-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="ecef5-156">Hodnoty patří `_prod` přípona k odlišení od `_dev` přípony hodnoty načteny ve vývojovém prostředí od tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="ecef5-157">Nahraďte `{KEY VAULT NAME}` s názvem služby key vault, kterou jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="ecef5-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="ecef5-158">ID aplikace a X.509 certifikát použít pro jiné Azure hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="ecef5-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="ecef5-159">Konfigurace služby Azure AD, Azure Key Vault a aplikace použít Azure Active Directory ID aplikace a X.509 certifikátu ověřování do služby key vault **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="ecef5-160">Další informace najdete v tématu [informace o klíčích, tajných kódů a certifikátů](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="ecef5-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="ecef5-161">I když se pomocí ID aplikace a X.509 certifikátu se nepodporují pro aplikace hostované v Azure, doporučujeme používat [spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) při hostování aplikace v Azure.</span><span class="sxs-lookup"><span data-stu-id="ecef5-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="ecef5-162">Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="ecef5-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="ecef5-163">Tato ukázková aplikace používá ID aplikace a služby X.509 certifikátu při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="ecef5-164">Vytvořit archiv PKCS #12 (*.pfx*) certifikát.</span><span class="sxs-lookup"><span data-stu-id="ecef5-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="ecef5-165">Možnosti pro vytváření certifikátů zahrnují [MakeCert na Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="ecef5-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="ecef5-166">Nainstalujte certifikát do úložiště osobních certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="ecef5-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="ecef5-167">Označit klíč jako exportovatelný je volitelný.</span><span class="sxs-lookup"><span data-stu-id="ecef5-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="ecef5-168">Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="ecef5-169">Export archivu PKCS #12 (*.pfx*) certifikát jako certifikát kódování DER (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="ecef5-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="ecef5-170">Registrace aplikace v Azure AD (**registrace aplikací**).</span><span class="sxs-lookup"><span data-stu-id="ecef5-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="ecef5-171">Nahrajte certifikát kódování DER (*.cer*) do služby Azure AD:</span><span class="sxs-lookup"><span data-stu-id="ecef5-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="ecef5-172">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ecef5-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="ecef5-173">Přejděte do **certifikáty a tajné kódy**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="ecef5-174">Vyberte **nahrát certifikát** se nahrát certifikát, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="ecef5-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="ecef5-175">A *.cer*, *.pem*, nebo *.crt* certifikátu je přijatelné.</span><span class="sxs-lookup"><span data-stu-id="ecef5-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="ecef5-176">Store název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu v aplikaci prvku *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="ecef5-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="ecef5-177">Přejděte do **trezory klíčů** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ecef5-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="ecef5-178">Vyberte trezor klíčů, které jste vytvořili [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="ecef5-179">Vyberte **zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="ecef5-180">Vyberte **přidat nový**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-180">Select **Add new**.</span></span>
1. <span data-ttu-id="ecef5-181">Vyberte **vybrat objekt zabezpečení** a vybrat registrovaná aplikace podle názvu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="ecef5-182">Vyberte **vyberte** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="ecef5-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="ecef5-183">Otevřít **oprávnění tajného klíče** a zajišťují aplikaci s **získat** a **seznamu** oprávnění.</span><span class="sxs-lookup"><span data-stu-id="ecef5-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="ecef5-184">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-184">Select **OK**.</span></span>
1. <span data-ttu-id="ecef5-185">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="ecef5-185">Select **Save**.</span></span>
1. <span data-ttu-id="ecef5-186">Nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-186">Deploy the app.</span></span>

<span data-ttu-id="ecef5-187">`Certificate` Ukázkové aplikace získá jeho hodnoty konfigurace z `IConfigurationRoot` se stejným názvem jako název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="ecef5-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="ecef5-188">Hierarchické bez hodnoty: Hodnota pro `SecretName` se získá pomocí `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="ecef5-189">Hierarchické hodnoty (oddílů): Použití `:` zápis (dvojtečka) nebo `GetSection` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="ecef5-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="ecef5-190">Použijte některou z těchto přístupů k získání hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ecef5-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="ecef5-191">Certifikát X.509 se spravuje přes operační systém.</span><span class="sxs-lookup"><span data-stu-id="ecef5-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="ecef5-192">Volání aplikace `AddAzureKeyVault` hodnotami poskytnutých *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="ecef5-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="ecef5-193">Ukázkové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="ecef5-193">Example values:</span></span>

* <span data-ttu-id="ecef5-194">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="ecef5-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="ecef5-195">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="ecef5-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="ecef5-196">Kryptografický otisk certifikátu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="ecef5-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="ecef5-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ecef5-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="ecef5-198">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ecef5-199">Ve vývojovém prostředí načíst hodnoty tajných kódů se `_dev` příponu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="ecef5-200">V produkčním prostředí a hodnoty načíst data pomocí `_prod` příponu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="ecef5-201">Použití spravované identity pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="ecef5-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="ecef5-202">**Aplikace nasazené do Azure** můžou těžit z výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), který umožňuje aplikaci k ověřování pomocí Azure Key Vault pomocí ověřování Azure AD bez pověření (ID aplikace a Tajný kód Password/Client) uložených v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ecef5-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="ecef5-203">Ukázková aplikace používá identity spravované pro prostředky Azure při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Managed`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="ecef5-204">Zadejte název trezoru do aplikace *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="ecef5-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="ecef5-205">Ukázková aplikace nevyžaduje, aby aplikace ID a heslo (tajný klíč klienta), pokud je nastavena na `Managed` verze, takže tyto položky konfigurace, můžete ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ecef5-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="ecef5-206">Nasazení aplikace do Azure a Azure ověřuje aplikaci přístup k použití pouze na název trezoru uložených v Azure Key Vault *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="ecef5-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="ecef5-207">Nasazení ukázkové aplikace do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ecef5-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="ecef5-208">Aplikace nasazené do služby Azure App Service při vytvoření služby Azure AD automaticky zaregistruje.</span><span class="sxs-lookup"><span data-stu-id="ecef5-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="ecef5-209">Získání ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="ecef5-210">ID objektu se zobrazí na webu Azure Portal na **Identity** panel služby App Service.</span><span class="sxs-lookup"><span data-stu-id="ecef5-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="ecef5-211">Pomocí Azure CLI a ID objektu aplikace, zadejte tyto aplikace s `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="ecef5-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="ecef5-212">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure, Powershellu nebo na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="ecef5-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="ecef5-213">Ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="ecef5-213">The sample app:</span></span>

* <span data-ttu-id="ecef5-214">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="ecef5-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="ecef5-215">Když řetězec připojení není k dispozici, zprostředkovatel pokusy o získání přístupového tokenu ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="ecef5-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="ecef5-216">Nový `KeyVaultClient` se vytvoří s `AzureServiceTokenProvider` instance tokenu zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="ecef5-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="ecef5-217">`KeyVaultClient` Instance se používá s výchozí implementaci třídy `IKeyVaultSecretManager` , který načte všechny hodnoty tajných kódů a nahradí double pomlčky (`--`) pomocí dvojtečky (`:`) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="ecef5-218">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-218">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ecef5-219">Ve vývojovém prostředí mají hodnoty tajných kódů `_dev` příponu, protože jste poskytované tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-219">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="ecef5-220">V produkčním prostředí a hodnoty načíst data pomocí `_prod` přípony vzhledem k tomu, že poskytovaný Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ecef5-220">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="ecef5-221">Pokud se zobrazí `Access denied` chyba, zkontrolujte, jestli je aplikace v Azure AD zaregistrováno a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-221">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="ecef5-222">Potvrďte, že jste restartovat službu v Azure.</span><span class="sxs-lookup"><span data-stu-id="ecef5-222">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="ecef5-223">Použijte předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="ecef5-223">Use a key name prefix</span></span>

<span data-ttu-id="ecef5-224">`AddAzureKeyVault` poskytuje přetížení přijímající implementace `IKeyVaultSecretManager`, což vám umožňuje řídit jak klíče trezoru tajných kódů se převedou na konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="ecef5-224">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="ecef5-225">Například můžete implementovat rozhraní za účelem načtení hodnoty tajných kódů na základě hodnoty předpony, které poskytnete při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-225">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="ecef5-226">Díky tomu můžete například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-226">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="ecef5-227">Nepoužívejte předpony v tajných kódů služby key vault, umístí tajných kódů pro více aplikací do stejného trezoru klíčů nebo umístit prostředí tajné kódy (například *vývoj* oproti *produkční* tajné kódy) do jedné trezor.</span><span class="sxs-lookup"><span data-stu-id="ecef5-227">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="ecef5-228">Doporučujeme různých aplikací a vývojové nebo produkční prostředí použít samostatné trezorům klíčů k izolování aplikace prostředí pro nejvyšší úroveň zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="ecef5-228">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="ecef5-229">V následujícím příkladu se v klíči naváže tajného kódu trezoru (a pomocí nástroje Správce tajný klíč pro vývojové prostředí) pro `5000-AppSecret` (tečky nejsou povoleny v názvech tajných kódů služby key vault).</span><span class="sxs-lookup"><span data-stu-id="ecef5-229">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="ecef5-230">Tento tajný kód představuje tajný kód aplikace pro 5.0.0.0 verzi aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-230">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="ecef5-231">Pro jinou verzi aplikace, 5.1.0.0, tajného kódu se přidá do klíče trezoru (a pomocí nástroje Správce tajný klíč) pro `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-231">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="ecef5-232">Jednotlivé verze aplikace načte do jeho konfigurace jako jeho verzované tajná hodnota `AppSecret`, vypuzovacího vypnout verze během načítání tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="ecef5-232">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="ecef5-233">`AddAzureKeyVault` je volána s vlastní `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="ecef5-233">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="ecef5-234">`IKeyVaultSecretManager` Implementace jsou reaguje na verze předpony tajných kódů k načtení správné tajného klíče do konfigurace:</span><span class="sxs-lookup"><span data-stu-id="ecef5-234">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="ecef5-235">`Load` Metoda je volána metodou poskytovatele algoritmus, který Iteruje přes našli ty, které mají předponu verze tajné klíče trezoru.</span><span class="sxs-lookup"><span data-stu-id="ecef5-235">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="ecef5-236">Když se nachází verze předpony s `Load`, používá algoritmus `GetKey` metoda vrátí název konfigurace název tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="ecef5-236">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="ecef5-237">Odstraní vypnout verze předpony z názvu tajného klíče a vrátí zbytek název tajného kódu pro načtení do konfigurace aplikace dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="ecef5-237">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="ecef5-238">Když je tento přístup implementovat:</span><span class="sxs-lookup"><span data-stu-id="ecef5-238">When this approach is implemented:</span></span>

1. <span data-ttu-id="ecef5-239">Verze aplikace zadaná v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-239">The app's version specified in the app's project file.</span></span> <span data-ttu-id="ecef5-240">V následujícím příkladu, verze aplikace nastavená na `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="ecef5-240">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ecef5-241">Ujistěte se, že `<UserSecretsId>` vlastnost je k dispozici v souboru projektu vaší aplikace, kde `{GUID}` je uživatelský identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="ecef5-241">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="ecef5-242">Uložte tyto tajné kódy místně [nástroj tajný klíč správce](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="ecef5-242">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="ecef5-243">Tajné klíče jsou uložené ve službě Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="ecef5-243">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="ecef5-244">Při spuštění aplikace se načítají tajných kódů služby key vault.</span><span class="sxs-lookup"><span data-stu-id="ecef5-244">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="ecef5-245">Řetězec tajný klíč pro `5000-AppSecret` odpovídá verzi aplikace zadanou v souboru projektu vaší aplikace (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="ecef5-245">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="ecef5-246">Verze, `5000` (s pomlčkou), je odebrána od názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="ecef5-246">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="ecef5-247">V celé aplikaci, čte se konfigurace s klíčem `AppSecret` načte hodnota tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="ecef5-247">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="ecef5-248">Pokud verze aplikace se změnilo v souboru projektu se `5.1.0.0` a aplikace znovu spustí, je vrácena hodnota tajného klíče `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="ecef5-248">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="ecef5-249">Můžete taky zadat vlastní `KeyVaultClient` implementaci `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-249">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="ecef5-250">Vlastního klienta umožňuje sdílení jedné instance klienta aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecef5-250">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ecef5-251">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="ecef5-251">Bind an array to a class</span></span>

<span data-ttu-id="ecef5-252">Zprostředkovatel je schopný načíst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="ecef5-252">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="ecef5-253">Při čtení ze zdroje konfigurace, která umožňuje klíče obsahovat dvojtečku (`:`) oddělovače, číselné klíčové segment, který se používá k rozlišení klíčů, které tvoří pole (`:0:`, `:1:`;...</span><span class="sxs-lookup"><span data-stu-id="ecef5-253">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="ecef5-254">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="ecef5-254">`:{n}:`).</span></span> <span data-ttu-id="ecef5-255">Další informace najdete v tématu [konfigurace: Svázat pole třídy](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="ecef5-255">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="ecef5-256">Azure Key Vault klíče nelze použít dvojtečku jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="ecef5-256">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="ecef5-257">Postupu popsaného v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="ecef5-257">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="ecef5-258">Pole klíče jsou uložené ve službě Azure Key Vault s double pomlčky a číselné klíčových segmentů (`--0--`, `--1--`, &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="ecef5-258">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="ecef5-259">Zkontrolujte následující [Serilog](https://serilog.net/) protokolování konfigurace poskytovatele poskytovaný souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="ecef5-259">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="ecef5-260">Existují dva literály definované v objektu `WriteTo` pole, které zahrnují dva Serilog *jímky*, které popisují cíle pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="ecef5-260">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="ecef5-261">Konfigurace je znázorněno v předchozím soubor JSON je uložená ve službě Azure Key Vault pomocí dvojitá čárka (`--`) zápisem a číselné segmenty:</span><span class="sxs-lookup"><span data-stu-id="ecef5-261">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="ecef5-262">Key</span><span class="sxs-lookup"><span data-stu-id="ecef5-262">Key</span></span> | <span data-ttu-id="ecef5-263">Value</span><span class="sxs-lookup"><span data-stu-id="ecef5-263">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="ecef5-264">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="ecef5-264">Reload secrets</span></span>

<span data-ttu-id="ecef5-265">Tajné klíče jsou uložené v mezipaměti až do `IConfigurationRoot.Reload()` je volána.</span><span class="sxs-lookup"><span data-stu-id="ecef5-265">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="ecef5-266">Vypršela platnost, zakázané, a aktualizované tajné kódy ve službě key vault není respektována aplikace do `Reload` provádí.</span><span class="sxs-lookup"><span data-stu-id="ecef5-266">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="ecef5-267">Tajné kódy zakázaná a jejichž platnost vypršela</span><span class="sxs-lookup"><span data-stu-id="ecef5-267">Disabled and expired secrets</span></span>

<span data-ttu-id="ecef5-268">Vyvolat zakázaná a jejichž platnost vypršela tajných kódů `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="ecef5-268">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="ecef5-269">Abyste zabránili vyvolání vaší aplikace, nahradit aplikaci nebo aktualizovat tajný kód zakázaný nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="ecef5-269">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ecef5-270">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="ecef5-270">Troubleshoot</span></span>

<span data-ttu-id="ecef5-271">Když aplikaci se pak nepodaří načíst konfiguraci pomocí zprostředkovatele, chybová zpráva je zapsána do [ASP.NET Core protokolování infrastruktury](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ecef5-271">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ecef5-272">Konfigurace načítání nebudou moct tyto podmínky:</span><span class="sxs-lookup"><span data-stu-id="ecef5-272">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="ecef5-273">Aplikace nebo certifikát není správně nakonfigurovaný v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ecef5-273">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="ecef5-274">Trezor klíčů neexistuje ve službě Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ecef5-274">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="ecef5-275">Aplikace nemá oprávnění pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="ecef5-275">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="ecef5-276">Zásady přístupu neobsahuje `Get` a `List` oprávnění.</span><span class="sxs-lookup"><span data-stu-id="ecef5-276">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="ecef5-277">Ve službě key vault konfigurační data (dvojice název hodnota) je nesprávně pojmenované, chybí, zakázán, nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="ecef5-277">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="ecef5-278">Aplikace má název chybný trezoru klíčů (`KeyVaultName`), Id aplikace Azure AD (`AzureADApplicationId`), nebo kryptografický otisk certifikátu služby Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="ecef5-278">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="ecef5-279">Konfigurační klíč (název) je nesprávný v aplikaci pro hodnotu, kterou se pokoušíte načíst.</span><span class="sxs-lookup"><span data-stu-id="ecef5-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ecef5-280">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ecef5-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="ecef5-281">Microsoft Azure: Trezor klíčů</span><span class="sxs-lookup"><span data-stu-id="ecef5-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="ecef5-282">Microsoft Azure: Dokumentace ke službě Key Vault</span><span class="sxs-lookup"><span data-stu-id="ecef5-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="ecef5-283">Postup generování a přenos chráněných pomocí HSM klíčů pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ecef5-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="ecef5-284">Třída KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="ecef5-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="ecef5-285">Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="ecef5-285">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="ecef5-286">Kurz: Jak používat Azure Key Vault s Windows virtuální počítač Azure v .NET</span><span class="sxs-lookup"><span data-stu-id="ecef5-286">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
