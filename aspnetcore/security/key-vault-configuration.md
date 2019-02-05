---
title: Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat aplikaci pomocí dvojice název hodnota v době běhu načteny pomocí zprostředkovatele konfigurace trezoru klíčů Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 8e40c8308a692731e71fb8ebebfc64e606874290
ms.sourcegitcommit: 98e9c7187772d4ddefe6d8e85d0d206749dbd2ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55737652"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="91e28-103">Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91e28-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="91e28-104">Podle [Luke Latham](https://github.com/guardrex) a [Andrew Stanton sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="91e28-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="91e28-105">Tento dokument popisuje, jak používat [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytovatel konfigurace pro načtení hodnoty konfigurace aplikace z Azure Key Vault tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="91e28-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="91e28-106">Azure Key Vault je Cloudová služba, která pomáhá zajistit ochranu kryptografických klíčů a tajných kódů používaných aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="91e28-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="91e28-107">Časté scénáře pro používání služby Azure Key Vault s aplikací ASP.NET Core patří:</span><span class="sxs-lookup"><span data-stu-id="91e28-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="91e28-108">Řízení přístupu k citlivým konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="91e28-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="91e28-109">Splnění požadavků pro FIPS 140-2 úrovně 2 ověřit modulů hardwarového zabezpečení (HSM) při ukládání konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="91e28-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="91e28-110">Tento scénář je k dispozici pro aplikace, které cílí ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="91e28-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="91e28-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91e28-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="91e28-112">Balíčky</span><span class="sxs-lookup"><span data-stu-id="91e28-112">Packages</span></span>

<span data-ttu-id="91e28-113">Použití zprostředkovatele konfigurace trezoru klíčů Azure, přidejte odkaz na balíček [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="91e28-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="91e28-114">Přijmout scénář identita spravované služby Azure, přidejte odkaz na balíček [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="91e28-114">To adopt the Azure Managed Service Identity scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="91e28-115">V době psaní, nejnovější stabilní verzi `Microsoft.Azure.Services.AppAuthentication`, verze `1.0.3`, poskytuje podporu pro [systém přiřadil spravovaných identit](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="91e28-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="91e28-116">Podpora pro *uživatelsky přiřazené identity spravované* je k dispozici v `1.0.2-preview` balíčku.</span><span class="sxs-lookup"><span data-stu-id="91e28-116">Support for *user-assigned managed identities* is available in the `1.0.2-preview` package.</span></span> <span data-ttu-id="91e28-117">Toto téma popisuje použití identit spravovaných systému a připravená ukázková aplikace používá verzi `1.0.3` z `Microsoft.Azure.Services.AppAuthentication` balíčku.</span><span class="sxs-lookup"><span data-stu-id="91e28-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="91e28-118">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="91e28-118">Sample app</span></span>

<span data-ttu-id="91e28-119">Ukázková aplikace spouští v jednom ze dvou režimů, které jsou určeny `#define` příkazu v horní části *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="91e28-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="91e28-120">`Basic` &ndash; Ukazuje použití ID aplikace klíč trezoru Azure a heslo (tajný klíč klienta), pro přístup k tajnými kódy uloženými v trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-120">`Basic` &ndash; Demonstrates the use of an Azure Key Vault Application ID and Password (Client Secret) to access secrets stored in the key vault.</span></span> <span data-ttu-id="91e28-121">Nasazení `Basic` verzi ukázky na libovolného hostitele, která je schopná obsluhovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91e28-121">Deploy the `Basic` version of the sample to any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="91e28-122">`Managed` &ndash; Ukazuje, jak používat Azure [Identity spravované služby (MSI)](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do služby Azure Key Vault pomocí ověřování Azure AD bez přihlašovací údaje uložené v kódu nebo konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-122">`Managed` &ndash; Demonstrates how to use Azure's [Managed Service Identity (MSI)](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="91e28-123">Při použití MSI pro ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) se nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="91e28-123">When using MSI to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="91e28-124">`Managed` Verzi vzorku se musí nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="91e28-124">The `Managed` version of the sample must be deployed to Azure.</span></span>

<span data-ttu-id="91e28-125">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktivy preprocesoru (`#define`), najdete v článku <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="91e28-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="91e28-126">Úložiště tajných kódů ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="91e28-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="91e28-127">Nastavení tajných kódů pomocí místně [nástroj tajný klíč správce](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="91e28-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="91e28-128">Spuštění ukázkové aplikace v místním počítači, ve vývojovém prostředí jsou načteny tajné klíče z místního úložiště tajný klíč správce.</span><span class="sxs-lookup"><span data-stu-id="91e28-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="91e28-129">Vyžaduje nástroj tajný klíč správce `<UserSecretsId>` vlastnost v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="91e28-130">Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="91e28-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="91e28-131">Tajné kódy jsou vytvořeny jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="91e28-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="91e28-132">Použít hierarchická hodnoty (konfigurační oddíly funkce) `:` (dvojtečka) jako oddělovač v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index) názvů klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="91e28-133">Tajný klíč správce se používá z příkazové okno otevřít obsahu kořen projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="91e28-133">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="91e28-134">Spusťte následující příkazy v příkazovém řádku z obsahu kořenového adresáře projektu k nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="91e28-134">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="91e28-135">Když tyto tajné kódy jsou uložené ve službě Azure Key Vault v [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) části `_dev` přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="91e28-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="91e28-136">Přípona poskytuje vizuální upozornění ve výstupu aplikace označující zdrojové hodnoty konfigurace.</span><span class="sxs-lookup"><span data-stu-id="91e28-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="91e28-137">Úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91e28-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="91e28-138">Podle pokynů uvedených [rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI](/azure/key-vault/quick-create-cli) tématu této části jsou shrnuty pro vytvoření služby Azure Key Vault a uložení tajných kódů používaných ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91e28-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="91e28-139">Viz téma pro další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="91e28-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="91e28-140">Otevřete Azure Cloud shell pomocí některého z následujících metod v [webu Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="91e28-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="91e28-141">Vyberte **vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="91e28-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="91e28-142">Použijte hledaný řetězec "Azure CLI" v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="91e28-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="91e28-143">Otevřete Cloud Shell v prohlížeči s **spustit Cloud Shell** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="91e28-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="91e28-144">Vyberte **Cloud Shell** tlačítko v pravém horním rohu webu Azure portal v nabídce.</span><span class="sxs-lookup"><span data-stu-id="91e28-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="91e28-145">Další informace najdete v tématu [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) a [Přehled služby Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="91e28-145">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="91e28-146">Pokud již nejste ověřeni, přihlaste se `az login` příkazu.</span><span class="sxs-lookup"><span data-stu-id="91e28-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="91e28-147">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro nové skupiny prostředků a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="91e28-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="91e28-148">Vytvoření trezoru klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název pro nový trezor klíčů a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="91e28-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="91e28-149">Vytvoření tajných kódů v trezoru klíčů jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="91e28-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="91e28-150">Azure Key Vault tajný názvy jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="91e28-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="91e28-151">Hierarchické hodnoty (konfigurační oddíly) použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="91e28-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="91e28-152">Použití dvojteček, které se obvykle používají pro vymezení část z podklíč v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v názvech tajného kódu trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="91e28-153">Proto jsou použít dvě pomlčky a má Prohodit pro dvojtečkou tajné klíče jsou načtena do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="91e28-154">Tyto tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="91e28-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="91e28-155">Hodnoty patří `_prod` přípona k odlišení od `_dev` přípony hodnoty načteny ve vývojovém prostředí od tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="91e28-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="91e28-156">Nahraďte `{KEY VAULT NAME}` s názvem služby key vault, kterou jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="91e28-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret"></a><span data-ttu-id="91e28-157">Pomocí ID aplikace a tajný kód klienta</span><span class="sxs-lookup"><span data-stu-id="91e28-157">Use Application ID and Client Secret</span></span>

<span data-ttu-id="91e28-158">Konfigurace služby Azure AD, Azure Key Vault a aplikace použít ID aplikace a heslo (tajný klíč klienta) k ověření do trezoru klíčů, když je aplikace hostovaná mimo Azure.</span><span class="sxs-lookup"><span data-stu-id="91e28-158">Configure Azure AD, Azure Key Vault, and the app to use an Application ID and Password (Client Secret) to authenticate to a key vault when the app is hosted outside of Azure.</span></span>

> [!NOTE]
> <span data-ttu-id="91e28-159">Ačkoli použití ID aplikace a heslo (tajný klíč klienta) se nepodporují pro aplikace hostované v Azure, doporučujeme použít [zprostředkovatele Identity spravované služby (MSI)](#use-the-managed-service-identity-msi-provider) při hostování aplikace v Azure.</span><span class="sxs-lookup"><span data-stu-id="91e28-159">Although using an Application ID and Password (Client Secret) is supported for apps hosted in Azure, we recommend using the [Managed Service Identity (MSI) Provider](#use-the-managed-service-identity-msi-provider) when hosting an app in Azure.</span></span> <span data-ttu-id="91e28-160">MSI nevyžaduje, aby ukládání přihlašovacích údajů do aplikace ani její konfiguraci, tak se považuje obecně bezpečnější.</span><span class="sxs-lookup"><span data-stu-id="91e28-160">MSI doesn't require storing credentials in the app or its configuration, so it's regarded as a generally safer approach.</span></span>

<span data-ttu-id="91e28-161">Ukázková aplikace používá při aplikace ID a heslo (tajný klíč klienta) `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Basic`.</span><span class="sxs-lookup"><span data-stu-id="91e28-161">The sample app uses an Application ID and Password (Client Secret) when the `#define` statement at the top of the *Program.cs* file is set to `Basic`.</span></span>

1. <span data-ttu-id="91e28-162">Registrace aplikace v Azure AD a vytvořit heslo pro identitu aplikace (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="91e28-162">Register the app with Azure AD and establish a Password (Client Secret) for the app's identity.</span></span>
1. <span data-ttu-id="91e28-163">Název trezoru klíčů, ID aplikace a tajný kód hesla/klienta Store aplikace *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="91e28-163">Store the key vault name, Application ID, and Password/Client Secret in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="91e28-164">Přejděte do **trezory klíčů** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="91e28-164">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="91e28-165">Vyberte trezor klíčů, které jste vytvořili [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu.</span><span class="sxs-lookup"><span data-stu-id="91e28-165">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="91e28-166">Vyberte **zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="91e28-166">Select **Access policies**.</span></span>
1. <span data-ttu-id="91e28-167">Vyberte **přidat nový**.</span><span class="sxs-lookup"><span data-stu-id="91e28-167">Select **Add new**.</span></span>
1. <span data-ttu-id="91e28-168">Vyberte **vybrat objekt zabezpečení** a vybrat registrovaná aplikace podle názvu.</span><span class="sxs-lookup"><span data-stu-id="91e28-168">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="91e28-169">Vyberte **vyberte** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="91e28-169">Select the **Select** button.</span></span>
1. <span data-ttu-id="91e28-170">Otevřít **oprávnění tajného klíče** a zajišťují aplikaci s **získat** a **seznamu** oprávnění.</span><span class="sxs-lookup"><span data-stu-id="91e28-170">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="91e28-171">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="91e28-171">Select **OK**.</span></span>
1. <span data-ttu-id="91e28-172">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="91e28-172">Select **Save**.</span></span>
1. <span data-ttu-id="91e28-173">Nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-173">Deploy the app.</span></span>

<span data-ttu-id="91e28-174">`Basic` Ukázkové aplikace získá jeho hodnoty konfigurace z `IConfigurationRoot` se stejným názvem jako název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="91e28-174">The `Basic` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="91e28-175">Hierarchické bez hodnoty: Hodnota pro `SecretName` se získá pomocí `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="91e28-175">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="91e28-176">Hierarchické hodnoty (oddílů): Použití `:` zápis (dvojtečka) nebo `GetSection` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="91e28-176">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="91e28-177">Použijte některou z těchto přístupů k získání hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="91e28-177">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="91e28-178">Volání aplikace `AddAzureKeyVault` hodnotami poskytnutých *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="91e28-178">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

<span data-ttu-id="91e28-179">Ukázkové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="91e28-179">Example values:</span></span>

* <span data-ttu-id="91e28-180">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="91e28-180">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="91e28-181">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="91e28-181">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="91e28-182">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="91e28-182">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="91e28-183">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="91e28-183">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="91e28-184">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="91e28-184">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="91e28-185">Ve vývojovém prostředí načíst hodnoty tajných kódů se `_dev` příponu.</span><span class="sxs-lookup"><span data-stu-id="91e28-185">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="91e28-186">V produkčním prostředí a hodnoty načíst data pomocí `_prod` příponu.</span><span class="sxs-lookup"><span data-stu-id="91e28-186">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-the-managed-service-identity-msi-provider"></a><span data-ttu-id="91e28-187">Použití zprostředkovatele Identity (MSI) spravované služby</span><span class="sxs-lookup"><span data-stu-id="91e28-187">Use the Managed Service Identity (MSI) Provider</span></span>

<span data-ttu-id="91e28-188">Aplikace nasazené do Azure můžete využít výhodu z Identity spravované služby (MSI), který umožňuje aplikaci k ověřování pomocí Azure Key Vault pomocí ověřování Azure AD bez pověření (ID aplikace a tajný kód hesla/klienta) uložených v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="91e28-188">An app deployed to Azure can take advantage of Managed Service Identity (MSI), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="91e28-189">Ukázková aplikace používá MSI při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Managed`.</span><span class="sxs-lookup"><span data-stu-id="91e28-189">The sample app uses MSI when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="91e28-190">Zadejte název trezoru do aplikace *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="91e28-190">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="91e28-191">Ukázková aplikace nevyžaduje, aby aplikace ID a heslo (tajný klíč klienta), pokud je nastavena na `Managed` verze, takže tyto položky konfigurace, můžete ignorovat.</span><span class="sxs-lookup"><span data-stu-id="91e28-191">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="91e28-192">Nasazení aplikace do Azure a Azure ověřuje aplikaci přístup k použití pouze na název trezoru uložených v Azure Key Vault *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="91e28-192">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="91e28-193">Nasazení ukázkové aplikace do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="91e28-193">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="91e28-194">Aplikace nasazené do služby Azure App Service při vytvoření služby Azure AD automaticky zaregistruje.</span><span class="sxs-lookup"><span data-stu-id="91e28-194">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="91e28-195">Získání ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="91e28-195">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="91e28-196">ID objektu se zobrazí na webu Azure Portal na **Identity** panel služby App Service.</span><span class="sxs-lookup"><span data-stu-id="91e28-196">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="91e28-197">Pomocí Azure CLI a ID objektu aplikace, zadejte tyto aplikace s `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="91e28-197">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="91e28-198">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure, Powershellu nebo na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="91e28-198">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="91e28-199">Ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="91e28-199">The sample app:</span></span>

* <span data-ttu-id="91e28-200">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="91e28-200">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="91e28-201">Když řetězec připojení není k dispozici, poskytovatel se pokusí získat přístupový token z MSI.</span><span class="sxs-lookup"><span data-stu-id="91e28-201">When a connection string isn't provided, the provider attempts to obtain an access token from MSI.</span></span>
* <span data-ttu-id="91e28-202">Nový `KeyVaultClient` se vytvoří s `AzureServiceTokenProvider` instance tokenu zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="91e28-202">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="91e28-203">`KeyVaultClient` Instance se používá s výchozí implementaci třídy `IKeyVaultSecretManager` , který načte všechny hodnoty tajných kódů a nahradí double pomlčky (`--`) pomocí dvojtečky (`:`) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-203">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="91e28-204">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="91e28-204">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="91e28-205">Ve vývojovém prostředí mají hodnoty tajných kódů `_dev` příponu, protože jste poskytované tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="91e28-205">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="91e28-206">V produkčním prostředí a hodnoty načíst data pomocí `_prod` přípony vzhledem k tomu, že poskytovaný Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="91e28-206">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="91e28-207">Pokud se zobrazí `Access denied` chyba, zkontrolujte, jestli je aplikace v Azure AD zaregistrováno a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-207">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="91e28-208">Potvrďte, že jste restartovat službu v Azure.</span><span class="sxs-lookup"><span data-stu-id="91e28-208">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="91e28-209">Použijte předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="91e28-209">Use a key name prefix</span></span>

<span data-ttu-id="91e28-210">`AddAzureKeyVault` poskytuje přetížení přijímající implementace `IKeyVaultSecretManager`, což vám umožňuje řídit jak klíče trezoru tajných kódů se převedou na konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="91e28-210">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="91e28-211">Například můžete implementovat rozhraní za účelem načtení hodnoty tajných kódů na základě hodnoty předpony, které poskytnete při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-211">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="91e28-212">Díky tomu můžete například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-212">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="91e28-213">Nepoužívejte předpony v tajných kódů služby key vault, umístí tajných kódů pro více aplikací do stejného trezoru klíčů nebo umístit prostředí tajné kódy (například *vývoj* oproti *produkční* tajné kódy) do jedné trezor.</span><span class="sxs-lookup"><span data-stu-id="91e28-213">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="91e28-214">Doporučujeme různých aplikací a vývojové nebo produkční prostředí použít samostatné trezorům klíčů k izolování aplikace prostředí pro nejvyšší úroveň zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="91e28-214">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="91e28-215">V následujícím příkladu se v klíči naváže tajného kódu trezoru (a pomocí nástroje Správce tajný klíč pro vývojové prostředí) pro `5000-AppSecret` (tečky nejsou povoleny v názvech tajných kódů služby key vault).</span><span class="sxs-lookup"><span data-stu-id="91e28-215">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="91e28-216">Tento tajný kód představuje tajný kód aplikace pro 5.0.0.0 verzi aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-216">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="91e28-217">Pro jinou verzi aplikace, 5.1.0.0, tajného kódu se přidá do klíče trezoru (a pomocí nástroje Správce tajný klíč) pro `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="91e28-217">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="91e28-218">Jednotlivé verze aplikace načte do jeho konfigurace jako jeho verzované tajná hodnota `AppSecret`, vypuzovacího vypnout verze během načítání tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="91e28-218">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="91e28-219">`AddAzureKeyVault` je volána s vlastní `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="91e28-219">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="91e28-220">Poskytuje hodnoty pro název trezoru klíčů, ID aplikace a heslo (tajný klíč klienta) *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="91e28-220">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="91e28-221">Ukázkové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="91e28-221">Example values:</span></span>

* <span data-ttu-id="91e28-222">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="91e28-222">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="91e28-223">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="91e28-223">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="91e28-224">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="91e28-224">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="91e28-225">`IKeyVaultSecretManager` Implementace jsou reaguje na verze předpony tajných kódů k načtení správné tajného klíče do konfigurace:</span><span class="sxs-lookup"><span data-stu-id="91e28-225">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="91e28-226">`Load` Metoda je volána metodou poskytovatele algoritmus, který Iteruje přes našli ty, které mají předponu verze tajné klíče trezoru.</span><span class="sxs-lookup"><span data-stu-id="91e28-226">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="91e28-227">Když se nachází verze předpony s `Load`, používá algoritmus `GetKey` metoda vrátí název konfigurace název tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="91e28-227">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="91e28-228">Odstraní vypnout verze předpony z názvu tajného klíče a vrátí zbytek název tajného kódu pro načtení do konfigurace aplikace dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="91e28-228">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="91e28-229">Když je tento přístup implementovat:</span><span class="sxs-lookup"><span data-stu-id="91e28-229">When this approach is implemented:</span></span>

1. <span data-ttu-id="91e28-230">Verze aplikace zadaná v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-230">The app's version specified in the app's project file.</span></span> <span data-ttu-id="91e28-231">V následujícím příkladu, verze aplikace nastavená na `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="91e28-231">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="91e28-232">Ujistěte se, že `<UserSecretsId>` vlastnost je k dispozici v souboru projektu vaší aplikace, kde `{GUID}` je uživatelský identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="91e28-232">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="91e28-233">Uložte tyto tajné kódy místně [nástroj tajný klíč správce](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="91e28-233">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="91e28-234">Tajné klíče jsou uložené ve službě Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="91e28-234">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="91e28-235">Při spuštění aplikace se načítají tajných kódů služby key vault.</span><span class="sxs-lookup"><span data-stu-id="91e28-235">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="91e28-236">Řetězec tajný klíč pro `5000-AppSecret` odpovídá verzi aplikace zadanou v souboru projektu vaší aplikace (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="91e28-236">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="91e28-237">Verze, `5000` (s pomlčkou), je odebrána od názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="91e28-237">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="91e28-238">V celé aplikaci, čte se konfigurace s klíčem `AppSecret` načte hodnota tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="91e28-238">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="91e28-239">Pokud verze aplikace se změnilo v souboru projektu se `5.1.0.0` a aplikace znovu spustí, je vrácena hodnota tajného klíče `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="91e28-239">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="91e28-240">Můžete taky zadat vlastní `KeyVaultClient` implementaci `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="91e28-240">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="91e28-241">Vlastního klienta umožňuje sdílení jedné instance klienta aplikace.</span><span class="sxs-lookup"><span data-stu-id="91e28-241">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a><span data-ttu-id="91e28-242">Ověřování pomocí certifikátu X.509 do služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91e28-242">Authenticate to Azure Key Vault with an X.509 certificate</span></span>

<span data-ttu-id="91e28-243">Při vývoji aplikace rozhraní .NET Framework v prostředí, které podporuje certifikáty, můžete ověřovat do služby Azure Key Vault pomocí certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="91e28-243">When developing a .NET Framework app in an environment that supports certificates, you can authenticate to Azure Key Vault with an X.509 certificate.</span></span> <span data-ttu-id="91e28-244">Privátní klíč certifikátu X.509 se spravuje přes operační systém.</span><span class="sxs-lookup"><span data-stu-id="91e28-244">The X.509 certificate's private key is managed by the OS.</span></span> <span data-ttu-id="91e28-245">Další informace najdete v tématu [ověřování pomocí certifikátu místo tajného klíče klienta](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span><span class="sxs-lookup"><span data-stu-id="91e28-245">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span> <span data-ttu-id="91e28-246">Použití `AddAzureKeyVault` přetížení přijímající `X509Certificate2` (`_env` v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="91e28-246">Use the `AddAzureKeyVault` overload that accepts an `X509Certificate2` (`_env` in the following example :</span></span>

```csharp
var builtConfig = config.Build();

var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates
    .Find(X509FindType.FindByThumbprint, 
        config["CertificateThumbprint"], false);

config.AddAzureKeyVault(
    builtConfig["Vault"],
    builtConfig["ClientId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(context.HostingEnvironment.ApplicationName));

store.Close();
```

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="91e28-247">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="91e28-247">Bind an array to a class</span></span>

<span data-ttu-id="91e28-248">Zprostředkovatel je schopný načíst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="91e28-248">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="91e28-249">Při čtení ze zdroje konfigurace, která umožňuje klíče obsahovat dvojtečku (`:`) oddělovače, číselné klíčové segment, který se používá k rozlišení klíčů, které tvoří pole (`:0:`, `:1:`;...</span><span class="sxs-lookup"><span data-stu-id="91e28-249">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="91e28-250">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="91e28-250">`:{n}:`).</span></span> <span data-ttu-id="91e28-251">Další informace najdete v tématu [konfigurace: Svázat pole třídy](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="91e28-251">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="91e28-252">Azure Key Vault klíče nelze použít dvojtečku jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="91e28-252">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="91e28-253">Postupu popsaného v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="91e28-253">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="91e28-254">Pole klíče jsou uložené ve službě Azure Key Vault s double pomlčky a číselné klíčových segmentů (`--0--`, `--1--`;...</span><span class="sxs-lookup"><span data-stu-id="91e28-254">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, …</span></span> <span data-ttu-id="91e28-255">`--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="91e28-255">`--{n}--`).</span></span>

<span data-ttu-id="91e28-256">Zkontrolujte následující [Serilog](https://serilog.net/) protokolování konfigurace poskytovatele poskytovaný souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="91e28-256">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="91e28-257">Existují dva literály definované v objektu `WriteTo` pole, které zahrnují dva Serilog *jímky*, které popisují cíle pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="91e28-257">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="91e28-258">Konfigurace je znázorněno v předchozím soubor JSON je uložená ve službě Azure Key Vault pomocí dvojitá čárka (`--`) zápisem a číselné segmenty:</span><span class="sxs-lookup"><span data-stu-id="91e28-258">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="91e28-259">Key</span><span class="sxs-lookup"><span data-stu-id="91e28-259">Key</span></span> | <span data-ttu-id="91e28-260">Hodnota</span><span class="sxs-lookup"><span data-stu-id="91e28-260">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="91e28-261">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="91e28-261">Reload secrets</span></span>

<span data-ttu-id="91e28-262">Tajné klíče jsou uložené v mezipaměti až do `IConfigurationRoot.Reload()` je volána.</span><span class="sxs-lookup"><span data-stu-id="91e28-262">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="91e28-263">Vypršela platnost, zakázané, a aktualizované tajné kódy ve službě key vault není respektována aplikace do `Reload` provádí.</span><span class="sxs-lookup"><span data-stu-id="91e28-263">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="91e28-264">Tajné kódy zakázaná a jejichž platnost vypršela</span><span class="sxs-lookup"><span data-stu-id="91e28-264">Disabled and expired secrets</span></span>

<span data-ttu-id="91e28-265">Vyvolat zakázaná a jejichž platnost vypršela tajných kódů `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="91e28-265">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="91e28-266">Abyste zabránili vyvolání vaší aplikace, nahradit aplikaci nebo aktualizovat tajný kód zakázaný nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="91e28-266">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="91e28-267">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="91e28-267">Troubleshoot</span></span>

<span data-ttu-id="91e28-268">Když aplikaci se pak nepodaří načíst konfiguraci pomocí zprostředkovatele, chybová zpráva je zapsána do [ASP.NET Core protokolování infrastruktury](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="91e28-268">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="91e28-269">Konfigurace načítání nebudou moct tyto podmínky:</span><span class="sxs-lookup"><span data-stu-id="91e28-269">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="91e28-270">Aplikace není správně nakonfigurovaný v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="91e28-270">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="91e28-271">Trezor klíčů neexistuje ve službě Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="91e28-271">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="91e28-272">Aplikace nemá oprávnění pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="91e28-272">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="91e28-273">Zásady přístupu neobsahuje `Get` a `List` oprávnění.</span><span class="sxs-lookup"><span data-stu-id="91e28-273">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="91e28-274">Ve službě key vault konfigurační data (dvojice název hodnota) je nesprávně pojmenované, chybí, zakázán, nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="91e28-274">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="91e28-275">Aplikace má název chybný trezoru klíčů (`Vault`), Id aplikace Azure AD (`ClientId`), nebo klíč služby Azure AD (`ClientSecret`).</span><span class="sxs-lookup"><span data-stu-id="91e28-275">The app has the wrong key vault name (`Vault`), Azure AD App Id (`ClientId`), or Azure AD Key (`ClientSecret`).</span></span>
* <span data-ttu-id="91e28-276">Klíč služby Azure AD (`ClientSecret`) vypršela.</span><span class="sxs-lookup"><span data-stu-id="91e28-276">The Azure AD Key (`ClientSecret`) is expired.</span></span>
* <span data-ttu-id="91e28-277">Konfigurační klíč (název) je nesprávný v aplikaci pro hodnotu, kterou se pokoušíte načíst.</span><span class="sxs-lookup"><span data-stu-id="91e28-277">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91e28-278">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="91e28-278">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="91e28-279">Microsoft Azure: Trezor klíčů</span><span class="sxs-lookup"><span data-stu-id="91e28-279">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="91e28-280">Microsoft Azure: Dokumentace ke službě Key Vault</span><span class="sxs-lookup"><span data-stu-id="91e28-280">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="91e28-281">Postup generování a přenos chráněných pomocí HSM klíčů pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91e28-281">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="91e28-282">Třída KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="91e28-282">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
