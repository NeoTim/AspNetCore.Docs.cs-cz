---
title: Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat aplikaci pomocí dvojice název hodnota v době běhu načteny pomocí zprostředkovatele konfigurace trezoru klíčů Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/22/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 2188929d6f380327465e8ce0fd8ad659188416d3
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56743982"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="d0f95-103">Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0f95-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="d0f95-104">Podle [Luke Latham](https://github.com/guardrex) a [Andrew Stanton sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d0f95-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="d0f95-105">Tento dokument popisuje, jak používat [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytovatel konfigurace pro načtení hodnoty konfigurace aplikace z Azure Key Vault tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="d0f95-106">Azure Key Vault je Cloudová služba, která pomáhá zajistit ochranu kryptografických klíčů a tajných kódů používaných aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="d0f95-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="d0f95-107">Časté scénáře pro používání služby Azure Key Vault s aplikací ASP.NET Core patří:</span><span class="sxs-lookup"><span data-stu-id="d0f95-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="d0f95-108">Řízení přístupu k citlivým konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="d0f95-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="d0f95-109">Splnění požadavků pro FIPS 140-2 úrovně 2 ověřit modulů hardwarového zabezpečení (HSM) při ukládání konfigurační data.</span><span class="sxs-lookup"><span data-stu-id="d0f95-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="d0f95-110">Tento scénář je k dispozici pro aplikace, které cílí ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="d0f95-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="d0f95-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0f95-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="d0f95-112">Balíčky</span><span class="sxs-lookup"><span data-stu-id="d0f95-112">Packages</span></span>

<span data-ttu-id="d0f95-113">Použití zprostředkovatele konfigurace trezoru klíčů Azure, přidejte odkaz na balíček [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="d0f95-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="d0f95-114">Přijmout [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) scénář, přidejte odkaz na balíček [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="d0f95-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f95-115">V době psaní, nejnovější stabilní verzi `Microsoft.Azure.Services.AppAuthentication`, verze `1.0.3`, poskytuje podporu pro [systém přiřadil spravovaných identit](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="d0f95-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="d0f95-116">Podpora pro *uživatelsky přiřazené identity spravované* je k dispozici v `1.0.2-preview` balíčku.</span><span class="sxs-lookup"><span data-stu-id="d0f95-116">Support for *user-assigned managed identities* is available in the `1.0.2-preview` package.</span></span> <span data-ttu-id="d0f95-117">Toto téma popisuje použití identit spravovaných systému a připravená ukázková aplikace používá verzi `1.0.3` z `Microsoft.Azure.Services.AppAuthentication` balíčku.</span><span class="sxs-lookup"><span data-stu-id="d0f95-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d0f95-118">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="d0f95-118">Sample app</span></span>

<span data-ttu-id="d0f95-119">Ukázková aplikace spouští v jednom ze dvou režimů, které jsou určeny `#define` příkazu v horní části *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="d0f95-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="d0f95-120">`Basic` &ndash; Ukazuje použití ID aplikace klíč trezoru Azure a heslo (tajný klíč klienta), pro přístup k tajnými kódy uloženými v trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-120">`Basic` &ndash; Demonstrates the use of an Azure Key Vault Application ID and Password (Client Secret) to access secrets stored in the key vault.</span></span> <span data-ttu-id="d0f95-121">Nasazení `Basic` verzi ukázky na libovolného hostitele, která je schopná obsluhovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0f95-121">Deploy the `Basic` version of the sample to any host capable of serving an ASP.NET Core app.</span></span> <span data-ttu-id="d0f95-122">Postupujte podle pokynů v [pomocí ID aplikace a tajný kód klienta pro aplikace bez Azure hostované](#use-application-id-and-client-secret-for-non-azure-hosted-apps) oddílu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-122">Follow the guidance in the [Use Application ID and Client Secret for non-Azure-hosted apps](#use-application-id-and-client-secret-for-non-azure-hosted-apps) section.</span></span>
* <span data-ttu-id="d0f95-123">`Managed` &ndash; Ukazuje, jak používat [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do služby Azure Key Vault pomocí ověřování Azure AD bez přihlašovací údaje uložené v kódu nebo konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-123">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="d0f95-124">Při použití spravované identity k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) se nevyžadují.</span><span class="sxs-lookup"><span data-stu-id="d0f95-124">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="d0f95-125">`Managed` Verzi vzorku se musí nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="d0f95-125">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="d0f95-126">Postupujte podle pokynů v [pomocí spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) oddílu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-126">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="d0f95-127">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktivy preprocesoru (`#define`), najdete v článku <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="d0f95-127">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="d0f95-128">Úložiště tajných kódů ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="d0f95-128">Secret storage in the Development environment</span></span>

<span data-ttu-id="d0f95-129">Nastavení tajných kódů pomocí místně [nástroj tajný klíč správce](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d0f95-129">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d0f95-130">Spuštění ukázkové aplikace v místním počítači, ve vývojovém prostředí jsou načteny tajné klíče z místního úložiště tajný klíč správce.</span><span class="sxs-lookup"><span data-stu-id="d0f95-130">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="d0f95-131">Vyžaduje nástroj tajný klíč správce `<UserSecretsId>` vlastnost v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-131">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="d0f95-132">Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="d0f95-132">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="d0f95-133">Tajné kódy jsou vytvořeny jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0f95-133">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="d0f95-134">Použít hierarchická hodnoty (konfigurační oddíly funkce) `:` (dvojtečka) jako oddělovač v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index) názvů klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-134">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="d0f95-135">Tajný klíč správce se používá z příkazové okno otevřít obsahu kořen projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="d0f95-135">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="d0f95-136">Spusťte následující příkazy v příkazovém řádku z obsahu kořenového adresáře projektu k nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d0f95-136">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="d0f95-137">Když tyto tajné kódy jsou uložené ve službě Azure Key Vault v [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) části `_dev` přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-137">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="d0f95-138">Přípona poskytuje vizuální upozornění ve výstupu aplikace označující zdrojové hodnoty konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-138">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="d0f95-139">Úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0f95-139">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="d0f95-140">Podle pokynů uvedených [rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI](/azure/key-vault/quick-create-cli) tématu této části jsou shrnuty pro vytvoření služby Azure Key Vault a uložení tajných kódů používaných ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d0f95-140">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="d0f95-141">Viz téma pro další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="d0f95-141">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="d0f95-142">Otevřete Azure Cloud shell pomocí některého z následujících metod v [webu Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="d0f95-142">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="d0f95-143">Vyberte **vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-143">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="d0f95-144">Použijte hledaný řetězec "Azure CLI" v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="d0f95-144">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="d0f95-145">Otevřete Cloud Shell v prohlížeči s **spustit Cloud Shell** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="d0f95-145">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="d0f95-146">Vyberte **Cloud Shell** tlačítko v pravém horním rohu webu Azure portal v nabídce.</span><span class="sxs-lookup"><span data-stu-id="d0f95-146">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="d0f95-147">Další informace najdete v tématu [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) a [Přehled služby Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="d0f95-147">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="d0f95-148">Pokud již nejste ověřeni, přihlaste se `az login` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-148">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="d0f95-149">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro nové skupiny prostředků a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="d0f95-149">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d0f95-150">Vytvoření trezoru klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název pro nový trezor klíčů a `{LOCATION}` je oblast Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="d0f95-150">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d0f95-151">Vytvoření tajných kódů v trezoru klíčů jako dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0f95-151">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="d0f95-152">Azure Key Vault tajný názvy jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="d0f95-152">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="d0f95-153">Hierarchické hodnoty (konfigurační oddíly) použijte `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="d0f95-153">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d0f95-154">Použití dvojteček, které se obvykle používají pro vymezení část z podklíč v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v názvech tajného kódu trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-154">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="d0f95-155">Proto jsou použít dvě pomlčky a má Prohodit pro dvojtečkou tajné klíče jsou načtena do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-155">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="d0f95-156">Tyto tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="d0f95-156">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="d0f95-157">Hodnoty patří `_prod` přípona k odlišení od `_dev` přípony hodnoty načteny ve vývojovém prostředí od tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-157">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="d0f95-158">Nahraďte `{KEY VAULT NAME}` s názvem služby key vault, kterou jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="d0f95-158">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a><span data-ttu-id="d0f95-159">Pomocí ID aplikace a tajný kód klienta pro jiné Azure hostované aplikace</span><span class="sxs-lookup"><span data-stu-id="d0f95-159">Use Application ID and Client Secret for non-Azure-hosted apps</span></span>

<span data-ttu-id="d0f95-160">Konfigurace služby Azure AD, Azure Key Vault a aplikace použít ID aplikace a heslo (tajný klíč klienta) k ověření do služby key vault **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="d0f95-160">Configure Azure AD, Azure Key Vault, and the app to use an Application ID and Password (Client Secret) to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f95-161">Ačkoli použití ID aplikace a heslo (tajný klíč klienta) se nepodporují pro aplikace hostované v Azure, doporučujeme používat [spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) při hostování aplikace v Azure.</span><span class="sxs-lookup"><span data-stu-id="d0f95-161">Although using an Application ID and Password (Client Secret) is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="d0f95-162">Spravované identity nevyžaduje, aby ukládání přihlašovacích údajů do aplikace ani její konfiguraci, tak se považuje obecně bezpečnější.</span><span class="sxs-lookup"><span data-stu-id="d0f95-162">Managed identities doesn't require storing credentials in the app or its configuration, so it's regarded as a generally safer approach.</span></span>

<span data-ttu-id="d0f95-163">Ukázková aplikace používá při aplikace ID a heslo (tajný klíč klienta) `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Basic`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-163">The sample app uses an Application ID and Password (Client Secret) when the `#define` statement at the top of the *Program.cs* file is set to `Basic`.</span></span>

1. <span data-ttu-id="d0f95-164">Registrace aplikace v Azure AD a vytvořit heslo pro identitu aplikace (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="d0f95-164">Register the app with Azure AD and establish a Password (Client Secret) for the app's identity.</span></span>
1. <span data-ttu-id="d0f95-165">Název trezoru klíčů, ID aplikace a tajný kód hesla/klienta Store aplikace *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d0f95-165">Store the key vault name, Application ID, and Password/Client Secret in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="d0f95-166">Přejděte do **trezory klíčů** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="d0f95-166">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="d0f95-167">Vyberte trezor klíčů, které jste vytvořili [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-167">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="d0f95-168">Vyberte **zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="d0f95-168">Select **Access policies**.</span></span>
1. <span data-ttu-id="d0f95-169">Vyberte **přidat nový**.</span><span class="sxs-lookup"><span data-stu-id="d0f95-169">Select **Add new**.</span></span>
1. <span data-ttu-id="d0f95-170">Vyberte **vybrat objekt zabezpečení** a vybrat registrovaná aplikace podle názvu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-170">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="d0f95-171">Vyberte **vyberte** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="d0f95-171">Select the **Select** button.</span></span>
1. <span data-ttu-id="d0f95-172">Otevřít **oprávnění tajného klíče** a zajišťují aplikaci s **získat** a **seznamu** oprávnění.</span><span class="sxs-lookup"><span data-stu-id="d0f95-172">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="d0f95-173">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d0f95-173">Select **OK**.</span></span>
1. <span data-ttu-id="d0f95-174">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="d0f95-174">Select **Save**.</span></span>
1. <span data-ttu-id="d0f95-175">Nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-175">Deploy the app.</span></span>

<span data-ttu-id="d0f95-176">`Basic` Ukázkové aplikace získá jeho hodnoty konfigurace z `IConfigurationRoot` se stejným názvem jako název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="d0f95-176">The `Basic` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="d0f95-177">Hierarchické bez hodnoty: Hodnota pro `SecretName` se získá pomocí `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-177">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="d0f95-178">Hierarchické hodnoty (oddílů): Použití `:` zápis (dvojtečka) nebo `GetSection` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d0f95-178">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="d0f95-179">Použijte některou z těchto přístupů k získání hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d0f95-179">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="d0f95-180">Volání aplikace `AddAzureKeyVault` hodnotami poskytnutých *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="d0f95-180">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

<span data-ttu-id="d0f95-181">Ukázkové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d0f95-181">Example values:</span></span>

* <span data-ttu-id="d0f95-182">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d0f95-182">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d0f95-183">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d0f95-183">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d0f95-184">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="d0f95-184">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="d0f95-185">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d0f95-185">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="d0f95-186">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-186">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d0f95-187">Ve vývojovém prostředí načíst hodnoty tajných kódů se `_dev` příponu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-187">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="d0f95-188">V produkčním prostředí a hodnoty načíst data pomocí `_prod` příponu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-188">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="d0f95-189">Použití spravované identity pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="d0f95-189">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="d0f95-190">**Aplikace nasazené do Azure** můžou těžit z výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), který umožňuje aplikaci k ověřování pomocí Azure Key Vault pomocí ověřování Azure AD bez pověření (ID aplikace a Tajný kód Password/Client) uložených v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d0f95-190">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="d0f95-191">Ukázková aplikace používá identity spravované pro prostředky Azure při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Managed`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-191">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="d0f95-192">Zadejte název trezoru do aplikace *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d0f95-192">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="d0f95-193">Ukázková aplikace nevyžaduje, aby aplikace ID a heslo (tajný klíč klienta), pokud je nastavena na `Managed` verze, takže tyto položky konfigurace, můžete ignorovat.</span><span class="sxs-lookup"><span data-stu-id="d0f95-193">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="d0f95-194">Nasazení aplikace do Azure a Azure ověřuje aplikaci přístup k použití pouze na název trezoru uložených v Azure Key Vault *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="d0f95-194">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="d0f95-195">Nasazení ukázkové aplikace do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d0f95-195">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="d0f95-196">Aplikace nasazené do služby Azure App Service při vytvoření služby Azure AD automaticky zaregistruje.</span><span class="sxs-lookup"><span data-stu-id="d0f95-196">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="d0f95-197">Získání ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-197">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="d0f95-198">ID objektu se zobrazí na webu Azure Portal na **Identity** panel služby App Service.</span><span class="sxs-lookup"><span data-stu-id="d0f95-198">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="d0f95-199">Pomocí Azure CLI a ID objektu aplikace, zadejte tyto aplikace s `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="d0f95-199">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="d0f95-200">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure, Powershellu nebo na webu Azure portal.</span><span class="sxs-lookup"><span data-stu-id="d0f95-200">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="d0f95-201">Ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="d0f95-201">The sample app:</span></span>

* <span data-ttu-id="d0f95-202">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="d0f95-202">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="d0f95-203">Když řetězec připojení není k dispozici, zprostředkovatel pokusy o získání přístupového tokenu ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="d0f95-203">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="d0f95-204">Nový `KeyVaultClient` se vytvoří s `AzureServiceTokenProvider` instance tokenu zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d0f95-204">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="d0f95-205">`KeyVaultClient` Instance se používá s výchozí implementaci třídy `IKeyVaultSecretManager` , který načte všechny hodnoty tajných kódů a nahradí double pomlčky (`--`) pomocí dvojtečky (`:`) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-205">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="d0f95-206">Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-206">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d0f95-207">Ve vývojovém prostředí mají hodnoty tajných kódů `_dev` příponu, protože jste poskytované tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-207">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="d0f95-208">V produkčním prostředí a hodnoty načíst data pomocí `_prod` přípony vzhledem k tomu, že poskytovaný Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d0f95-208">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="d0f95-209">Pokud se zobrazí `Access denied` chyba, zkontrolujte, jestli je aplikace v Azure AD zaregistrováno a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-209">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="d0f95-210">Potvrďte, že jste restartovat službu v Azure.</span><span class="sxs-lookup"><span data-stu-id="d0f95-210">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="d0f95-211">Použijte předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="d0f95-211">Use a key name prefix</span></span>

<span data-ttu-id="d0f95-212">`AddAzureKeyVault` poskytuje přetížení přijímající implementace `IKeyVaultSecretManager`, což vám umožňuje řídit jak klíče trezoru tajných kódů se převedou na konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="d0f95-212">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="d0f95-213">Například můžete implementovat rozhraní za účelem načtení hodnoty tajných kódů na základě hodnoty předpony, které poskytnete při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-213">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="d0f95-214">Díky tomu můžete například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-214">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="d0f95-215">Nepoužívejte předpony v tajných kódů služby key vault, umístí tajných kódů pro více aplikací do stejného trezoru klíčů nebo umístit prostředí tajné kódy (například *vývoj* oproti *produkční* tajné kódy) do jedné trezor.</span><span class="sxs-lookup"><span data-stu-id="d0f95-215">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="d0f95-216">Doporučujeme různých aplikací a vývojové nebo produkční prostředí použít samostatné trezorům klíčů k izolování aplikace prostředí pro nejvyšší úroveň zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="d0f95-216">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="d0f95-217">V následujícím příkladu se v klíči naváže tajného kódu trezoru (a pomocí nástroje Správce tajný klíč pro vývojové prostředí) pro `5000-AppSecret` (tečky nejsou povoleny v názvech tajných kódů služby key vault).</span><span class="sxs-lookup"><span data-stu-id="d0f95-217">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="d0f95-218">Tento tajný kód představuje tajný kód aplikace pro 5.0.0.0 verzi aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-218">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="d0f95-219">Pro jinou verzi aplikace, 5.1.0.0, tajného kódu se přidá do klíče trezoru (a pomocí nástroje Správce tajný klíč) pro `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-219">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="d0f95-220">Jednotlivé verze aplikace načte do jeho konfigurace jako jeho verzované tajná hodnota `AppSecret`, vypuzovacího vypnout verze během načítání tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="d0f95-220">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="d0f95-221">`AddAzureKeyVault` je volána s vlastní `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="d0f95-221">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="d0f95-222">Poskytuje hodnoty pro název trezoru klíčů, ID aplikace a heslo (tajný klíč klienta) *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="d0f95-222">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="d0f95-223">Ukázkové hodnoty:</span><span class="sxs-lookup"><span data-stu-id="d0f95-223">Example values:</span></span>

* <span data-ttu-id="d0f95-224">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d0f95-224">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d0f95-225">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d0f95-225">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d0f95-226">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="d0f95-226">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="d0f95-227">`IKeyVaultSecretManager` Implementace jsou reaguje na verze předpony tajných kódů k načtení správné tajného klíče do konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d0f95-227">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="d0f95-228">`Load` Metoda je volána metodou poskytovatele algoritmus, který Iteruje přes našli ty, které mají předponu verze tajné klíče trezoru.</span><span class="sxs-lookup"><span data-stu-id="d0f95-228">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="d0f95-229">Když se nachází verze předpony s `Load`, používá algoritmus `GetKey` metoda vrátí název konfigurace název tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="d0f95-229">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="d0f95-230">Odstraní vypnout verze předpony z názvu tajného klíče a vrátí zbytek název tajného kódu pro načtení do konfigurace aplikace dvojice název hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0f95-230">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="d0f95-231">Když je tento přístup implementovat:</span><span class="sxs-lookup"><span data-stu-id="d0f95-231">When this approach is implemented:</span></span>

1. <span data-ttu-id="d0f95-232">Verze aplikace zadaná v souboru projektu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-232">The app's version specified in the app's project file.</span></span> <span data-ttu-id="d0f95-233">V následujícím příkladu, verze aplikace nastavená na `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="d0f95-233">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d0f95-234">Ujistěte se, že `<UserSecretsId>` vlastnost je k dispozici v souboru projektu vaší aplikace, kde `{GUID}` je uživatelský identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="d0f95-234">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="d0f95-235">Uložte tyto tajné kódy místně [nástroj tajný klíč správce](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="d0f95-235">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="d0f95-236">Tajné klíče jsou uložené ve službě Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="d0f95-236">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="d0f95-237">Při spuštění aplikace se načítají tajných kódů služby key vault.</span><span class="sxs-lookup"><span data-stu-id="d0f95-237">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="d0f95-238">Řetězec tajný klíč pro `5000-AppSecret` odpovídá verzi aplikace zadanou v souboru projektu vaší aplikace (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="d0f95-238">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="d0f95-239">Verze, `5000` (s pomlčkou), je odebrána od názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="d0f95-239">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="d0f95-240">V celé aplikaci, čte se konfigurace s klíčem `AppSecret` načte hodnota tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="d0f95-240">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="d0f95-241">Pokud verze aplikace se změnilo v souboru projektu se `5.1.0.0` a aplikace znovu spustí, je vrácena hodnota tajného klíče `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="d0f95-241">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f95-242">Můžete taky zadat vlastní `KeyVaultClient` implementaci `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-242">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="d0f95-243">Vlastního klienta umožňuje sdílení jedné instance klienta aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0f95-243">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a><span data-ttu-id="d0f95-244">Ověřování pomocí certifikátu X.509 do služby Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0f95-244">Authenticate to Azure Key Vault with an X.509 certificate</span></span>

<span data-ttu-id="d0f95-245">Při vývoji aplikace rozhraní .NET Framework v prostředí, které podporuje certifikáty, můžete ověřovat do služby Azure Key Vault pomocí certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="d0f95-245">When developing a .NET Framework app in an environment that supports certificates, you can authenticate to Azure Key Vault with an X.509 certificate.</span></span> <span data-ttu-id="d0f95-246">Privátní klíč certifikátu X.509 se spravuje přes operační systém.</span><span class="sxs-lookup"><span data-stu-id="d0f95-246">The X.509 certificate's private key is managed by the OS.</span></span> <span data-ttu-id="d0f95-247">Další informace najdete v tématu [ověřování pomocí certifikátu místo tajného klíče klienta](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span><span class="sxs-lookup"><span data-stu-id="d0f95-247">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span> <span data-ttu-id="d0f95-248">Použití `AddAzureKeyVault` přetížení přijímající `X509Certificate2` (`_env` v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d0f95-248">Use the `AddAzureKeyVault` overload that accepts an `X509Certificate2` (`_env` in the following example :</span></span>

```csharp
var builtConfig = config.Build();

var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates
    .Find(X509FindType.FindByThumbprint, 
        config["CertificateThumbprint"], false);

config.AddAzureKeyVault(
    builtConfig["KeyVaultName"],
    builtConfig["AzureADApplicationId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(context.HostingEnvironment.ApplicationName));

store.Close();
```

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d0f95-249">Svázat pole třídy</span><span class="sxs-lookup"><span data-stu-id="d0f95-249">Bind an array to a class</span></span>

<span data-ttu-id="d0f95-250">Zprostředkovatel je schopný načíst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="d0f95-250">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="d0f95-251">Při čtení ze zdroje konfigurace, která umožňuje klíče obsahovat dvojtečku (`:`) oddělovače, číselné klíčové segment, který se používá k rozlišení klíčů, které tvoří pole (`:0:`, `:1:`;...</span><span class="sxs-lookup"><span data-stu-id="d0f95-251">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="d0f95-252">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="d0f95-252">`:{n}:`).</span></span> <span data-ttu-id="d0f95-253">Další informace najdete v tématu [konfigurace: Svázat pole třídy](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="d0f95-253">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="d0f95-254">Azure Key Vault klíče nelze použít dvojtečku jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="d0f95-254">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="d0f95-255">Postupu popsaného v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="d0f95-255">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="d0f95-256">Pole klíče jsou uložené ve službě Azure Key Vault s double pomlčky a číselné klíčových segmentů (`--0--`, `--1--`;...</span><span class="sxs-lookup"><span data-stu-id="d0f95-256">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, …</span></span> <span data-ttu-id="d0f95-257">`--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="d0f95-257">`--{n}--`).</span></span>

<span data-ttu-id="d0f95-258">Zkontrolujte následující [Serilog](https://serilog.net/) protokolování konfigurace poskytovatele poskytovaný souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="d0f95-258">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="d0f95-259">Existují dva literály definované v objektu `WriteTo` pole, které zahrnují dva Serilog *jímky*, které popisují cíle pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="d0f95-259">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="d0f95-260">Konfigurace je znázorněno v předchozím soubor JSON je uložená ve službě Azure Key Vault pomocí dvojitá čárka (`--`) zápisem a číselné segmenty:</span><span class="sxs-lookup"><span data-stu-id="d0f95-260">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="d0f95-261">Key</span><span class="sxs-lookup"><span data-stu-id="d0f95-261">Key</span></span> | <span data-ttu-id="d0f95-262">Hodnota</span><span class="sxs-lookup"><span data-stu-id="d0f95-262">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="d0f95-263">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="d0f95-263">Reload secrets</span></span>

<span data-ttu-id="d0f95-264">Tajné klíče jsou uložené v mezipaměti až do `IConfigurationRoot.Reload()` je volána.</span><span class="sxs-lookup"><span data-stu-id="d0f95-264">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="d0f95-265">Vypršela platnost, zakázané, a aktualizované tajné kódy ve službě key vault není respektována aplikace do `Reload` provádí.</span><span class="sxs-lookup"><span data-stu-id="d0f95-265">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="d0f95-266">Tajné kódy zakázaná a jejichž platnost vypršela</span><span class="sxs-lookup"><span data-stu-id="d0f95-266">Disabled and expired secrets</span></span>

<span data-ttu-id="d0f95-267">Vyvolat zakázaná a jejichž platnost vypršela tajných kódů `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="d0f95-267">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="d0f95-268">Abyste zabránili vyvolání vaší aplikace, nahradit aplikaci nebo aktualizovat tajný kód zakázaný nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="d0f95-268">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d0f95-269">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="d0f95-269">Troubleshoot</span></span>

<span data-ttu-id="d0f95-270">Když aplikaci se pak nepodaří načíst konfiguraci pomocí zprostředkovatele, chybová zpráva je zapsána do [ASP.NET Core protokolování infrastruktury](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d0f95-270">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d0f95-271">Konfigurace načítání nebudou moct tyto podmínky:</span><span class="sxs-lookup"><span data-stu-id="d0f95-271">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="d0f95-272">Aplikace není správně nakonfigurovaný v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d0f95-272">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="d0f95-273">Trezor klíčů neexistuje ve službě Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d0f95-273">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="d0f95-274">Aplikace nemá oprávnění pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="d0f95-274">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="d0f95-275">Zásady přístupu neobsahuje `Get` a `List` oprávnění.</span><span class="sxs-lookup"><span data-stu-id="d0f95-275">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="d0f95-276">Ve službě key vault konfigurační data (dvojice název hodnota) je nesprávně pojmenované, chybí, zakázán, nebo vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="d0f95-276">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="d0f95-277">Aplikace má název chybný trezoru klíčů (`KeyVaultName`), Id aplikace Azure AD (`AzureADApplicationId`), nebo hesel služby Azure AD (tajný klíč klienta) (`AzureADPassword`).</span><span class="sxs-lookup"><span data-stu-id="d0f95-277">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD Password (Client Secret) (`AzureADPassword`).</span></span>
* <span data-ttu-id="d0f95-278">Hesel služby Azure AD (tajný klíč klienta) (`AzureADPassword`) vypršela.</span><span class="sxs-lookup"><span data-stu-id="d0f95-278">The Azure AD Password (Client Secret) (`AzureADPassword`) is expired.</span></span>
* <span data-ttu-id="d0f95-279">Konfigurační klíč (název) je nesprávný v aplikaci pro hodnotu, kterou se pokoušíte načíst.</span><span class="sxs-lookup"><span data-stu-id="d0f95-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0f95-280">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d0f95-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="d0f95-281">Microsoft Azure: Trezor klíčů</span><span class="sxs-lookup"><span data-stu-id="d0f95-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="d0f95-282">Microsoft Azure: Dokumentace ke službě Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0f95-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="d0f95-283">Postup generování a přenos chráněných pomocí HSM klíčů pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d0f95-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="d0f95-284">Třída KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="d0f95-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
