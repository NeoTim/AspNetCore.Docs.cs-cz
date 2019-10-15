---
title: Poskytovatel konfigurace Azure Key Vault v ASP.NET Core
author: guardrex
description: Naučte se používat zprostředkovatele konfigurace Azure Key Vault ke konfiguraci aplikace pomocí dvojic název-hodnota načtené za běhu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2019
uid: security/key-vault-configuration
ms.openlocfilehash: c8e76068dbcf2a59a15fa75a1fc5aa0032e6acc5
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334197"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="1f8a3-103">Poskytovatel konfigurace Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f8a3-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="1f8a3-104">[Luke Latham](https://github.com/guardrex) a [Andrew Stanton – sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="1f8a3-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="1f8a3-105">Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="1f8a3-106">Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="1f8a3-107">Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="1f8a3-108">Řízení přístupu k citlivým datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="1f8a3-109">Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="1f8a3-110">Tento scénář je k dispozici pro aplikace cílené na ASP.NET Core 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="1f8a3-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f8a3-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="1f8a3-112">Balíčky</span><span class="sxs-lookup"><span data-stu-id="1f8a3-112">Packages</span></span>

<span data-ttu-id="1f8a3-113">Chcete-li použít poskytovatele konfigurace Azure Key Vault, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="1f8a3-114">Pokud chcete přijmout [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) , přidejte odkaz na balíček do balíčku [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="1f8a3-115">V době psaní nejnovější stabilní verze `Microsoft.Azure.Services.AppAuthentication`, verze `1.0.3`, poskytuje podporu pro [spravované identity přiřazené systémem](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="1f8a3-116">V balíčku `1.2.0-preview2` je dostupná podpora pro *uživatelem přiřazené spravované identity* .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="1f8a3-117">Toto téma ukazuje použití identit spravovaných systémem a zadaná ukázková aplikace používá verzi `1.0.3` balíčku `Microsoft.Azure.Services.AppAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="1f8a3-118">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="1f8a3-118">Sample app</span></span>

<span data-ttu-id="1f8a3-119">Ukázková aplikace běží v jednom ze dvou režimů určených příkazem `#define` v horní části souboru *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="1f8a3-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="1f8a3-120">`Certificate` &ndash; ukazuje použití Azure Key Vaultho ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="1f8a3-121">Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="1f8a3-122">`Managed` &ndash; ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace pro Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="1f8a3-123">Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="1f8a3-124">Ukázková verze `Managed` se musí nasadit do Azure.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="1f8a3-125">Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="1f8a3-126">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru (`#define`), najdete v tématu <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="1f8a3-127">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="1f8a3-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="1f8a3-128">Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1f8a3-129">Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="1f8a3-130">Nástroj Správce tajných klíčů vyžaduje vlastnost `<UserSecretsId>` v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="1f8a3-131">Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="1f8a3-132">Tajné kódy jsou vytvořeny jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="1f8a3-133">Hierarchické hodnoty (konfigurační oddíly) používají v názvech [ASP.NET Core konfiguračních](xref:fundamentals/configuration/index) klíčů `:` (dvojtečku) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="1f8a3-134">Správce tajného klíče se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-134">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="1f8a3-135">Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-135">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="1f8a3-136">Pokud jsou tyto tajné klíče uložené v Azure Key Vault [tajné úložiště v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílem, přípona `_dev` se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="1f8a3-137">Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="1f8a3-138">Tajné úložiště v produkčním prostředí pomocí Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1f8a3-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="1f8a3-139">Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="1f8a3-140">Další podrobnosti najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="1f8a3-141">Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="1f8a3-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="1f8a3-142">Vyberte **vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="1f8a3-143">V textovém poli použijte hledaný řetězec "Azure CLI".</span><span class="sxs-lookup"><span data-stu-id="1f8a3-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="1f8a3-144">Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="1f8a3-145">V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="1f8a3-146">Další informace najdete v tématu [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="1f8a3-147">Pokud jste to ještě neověřili, přihlaste se pomocí příkazu `az login`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="1f8a3-148">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` je oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="1f8a3-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1f8a3-149">Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` je oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="1f8a3-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1f8a3-150">Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="1f8a3-151">Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="1f8a3-152">Hierarchické hodnoty (konfigurační oddíly) používají jako oddělovač `--` (dvě pomlčky).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="1f8a3-153">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="1f8a3-154">Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="1f8a3-155">Následující tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="1f8a3-156">Hodnoty zahrnují příponu `_prod`, aby se lišily od hodnot přípony `_dev` načtených ve vývojovém prostředí od uživatelských tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="1f8a3-157">Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="1f8a3-158">Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure</span><span class="sxs-lookup"><span data-stu-id="1f8a3-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="1f8a3-159">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="1f8a3-160">Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="1f8a3-161">I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="1f8a3-162">Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="1f8a3-163">Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud je příkaz `#define` v horní části souboru *program.cs* nastavený na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="1f8a3-164">Vytvořte certifikát PKCS # 12 ( *. pfx*).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="1f8a3-165">Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="1f8a3-166">Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="1f8a3-167">Označení klíče jako exportovatelných je volitelné.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="1f8a3-168">Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="1f8a3-169">Exportujte certifikát PKCS # 12 ( *. pfx*) jako certifikát kódovaný v kódování DER ( *. cer*).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="1f8a3-170">Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="1f8a3-171">Nahrajte certifikát s kódováním DER ( *. cer*) do Azure AD:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="1f8a3-172">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="1f8a3-173">Přejděte na **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="1f8a3-174">Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="1f8a3-175">Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="1f8a3-176">V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="1f8a3-177">V Azure Portal přejděte na **trezory klíčů** .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="1f8a3-178">[V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="1f8a3-179">Vyberte **zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="1f8a3-180">Vyberte **Přidat nový**.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-180">Select **Add new**.</span></span>
1. <span data-ttu-id="1f8a3-181">Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="1f8a3-182">Vyberte tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="1f8a3-183">Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="1f8a3-184">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-184">Select **OK**.</span></span>
1. <span data-ttu-id="1f8a3-185">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-185">Select **Save**.</span></span>
1. <span data-ttu-id="1f8a3-186">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-186">Deploy the app.</span></span>

<span data-ttu-id="1f8a3-187">Ukázková aplikace `Certificate` získá své konfigurační hodnoty z `IConfigurationRoot` se stejným názvem, jako má název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="1f8a3-188">Hodnoty, které nejsou hierarchicky: hodnota pro `SecretName` se získá s `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="1f8a3-189">Hierarchické hodnoty (oddíly): použijte notaci `:` (dvojtečky) nebo metodu rozšíření `GetSection`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="1f8a3-190">K získání hodnoty konfigurace použijte některý z těchto přístupů:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="1f8a3-191">Certifikát X. 509 spravuje operační systém.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="1f8a3-192">Aplikace volá `AddAzureKeyVault` s hodnotami poskytnutými souborem *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1f8a3-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="1f8a3-193">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-193">Example values:</span></span>

* <span data-ttu-id="1f8a3-194">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="1f8a3-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="1f8a3-195">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="1f8a3-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="1f8a3-196">Kryptografický otisk certifikátu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="1f8a3-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="1f8a3-197">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="1f8a3-198">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1f8a3-199">Ve vývojovém prostředí se tajné hodnoty načítají s příponou `_dev`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="1f8a3-200">V produkčním prostředí se hodnoty načítají s příponou `_prod`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="1f8a3-201">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="1f8a3-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="1f8a3-202">**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo/tajný klíč klienta). Uloženo v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="1f8a3-203">Ukázková aplikace používá spravované identity pro prostředky Azure, pokud je příkaz `#define` v horní části souboru *program.cs* nastavený na `Managed`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="1f8a3-204">Do souboru *appSettings. JSON* aplikace zadejte název trezoru.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="1f8a3-205">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená na verzi `Managed`, takže tyto položky konfigurace můžete ignorovat.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="1f8a3-206">Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="1f8a3-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="1f8a3-207">Nasaďte ukázkovou aplikaci do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="1f8a3-208">Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="1f8a3-209">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="1f8a3-210">ID objektu se zobrazí v Azure Portal na panelu **identita** App Service.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="1f8a3-211">Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci s oprávněním @no__t 0 a `get` pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="1f8a3-212">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="1f8a3-213">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-213">The sample app:</span></span>

* <span data-ttu-id="1f8a3-214">Vytvoří instanci třídy `AzureServiceTokenProvider` bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="1f8a3-215">Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="1f8a3-216">Nový `KeyVaultClient` se vytvoří pomocí zpětného volání tokenu instance `AzureServiceTokenProvider`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="1f8a3-217">Instance `KeyVaultClient` se používá s výchozí implementací `IKeyVaultSecretManager`, která načte všechny tajné hodnoty a nahradí dvojité spojovníky (`--`) dvojtečkami (`:`) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="1f8a3-218">Ukázková hodnota názvu trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="1f8a3-218">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="1f8a3-219">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-219">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="1f8a3-220">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-220">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1f8a3-221">Ve vývojovém prostředí mají tajné hodnoty příponu `_dev`, protože jsou k dispozici prostřednictvím uživatelských tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-221">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="1f8a3-222">V produkčním prostředí se hodnoty načítají s příponou @no__t 0, protože jsou k dispozici v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-222">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="1f8a3-223">Pokud se zobrazí chyba `Access denied`, potvrďte, že je aplikace zaregistrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-223">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="1f8a3-224">Potvrďte, že jste službu restartovali v Azure.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-224">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="1f8a3-225">Použít předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="1f8a3-225">Use a key name prefix</span></span>

<span data-ttu-id="1f8a3-226">`AddAzureKeyVault` poskytuje přetížení, které přijímá implementaci `IKeyVaultSecretManager`, což vám umožní řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-226">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="1f8a3-227">Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-227">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="1f8a3-228">To vám umožní například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-228">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="1f8a3-229">Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-229">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="1f8a3-230">Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-230">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="1f8a3-231">V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (období nejsou v tajných názvech trezoru klíčů povolená).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-231">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="1f8a3-232">Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-232">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="1f8a3-233">V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-233">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="1f8a3-234">Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret`. při načítání tajného kódu se vymění jeho verze.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-234">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="1f8a3-235">`AddAzureKeyVault` se volá s vlastním `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-235">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="1f8a3-236">Implementace `IKeyVaultSecretManager` reaguje na předpony verze tajných klíčů, aby se do konfigurace načetl správný tajný kód:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-236">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="1f8a3-237">Metoda `Load` je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-237">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="1f8a3-238">Pokud je nalezena předpona verze s `Load`, algoritmus používá metodu `GetKey` k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-238">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="1f8a3-239">Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-239">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="1f8a3-240">Při implementaci tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-240">When this approach is implemented:</span></span>

1. <span data-ttu-id="1f8a3-241">Verze aplikace zadaná v souboru projektu aplikace</span><span class="sxs-lookup"><span data-stu-id="1f8a3-241">The app's version specified in the app's project file.</span></span> <span data-ttu-id="1f8a3-242">V následujícím příkladu je verze aplikace nastavená na `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-242">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="1f8a3-243">Ověřte, že se v souboru projektu aplikace nachází vlastnost `<UserSecretsId>`, kde `{GUID}` je uživatelem zadaný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-243">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="1f8a3-244">Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="1f8a3-244">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="1f8a3-245">Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-245">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="1f8a3-246">Po spuštění aplikace se načtou tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-246">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="1f8a3-247">Řetězec tajného kódu `5000-AppSecret` se shoduje s verzí aplikace zadanou v souboru projektu aplikace (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-247">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="1f8a3-248">Verze `5000` (s pomlčkou), je z názvu klíče odstraněna.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-248">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="1f8a3-249">V celé aplikaci čtení konfigurace pomocí klíče `AppSecret` načte tajnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-249">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="1f8a3-250">Pokud je verze aplikace v souboru projektu změněna na `5.1.0.0` a aplikace je znovu spuštěna, vrátí tajná hodnota `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-250">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="1f8a3-251">Můžete také poskytnout vlastní implementaci `KeyVaultClient` pro `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-251">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="1f8a3-252">Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-252">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="1f8a3-253">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="1f8a3-253">Bind an array to a class</span></span>

<span data-ttu-id="1f8a3-254">Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-254">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="1f8a3-255">Při čtení ze zdroje konfigurace, který umožňuje klíčům obsahovat oddělovače dvojtečky (`:`), se pro odlišení klíčů, které tvoří pole (`:0:`, `:1:`, používá číselný segment klíče...</span><span class="sxs-lookup"><span data-stu-id="1f8a3-255">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="1f8a3-256">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-256">`:{n}:`).</span></span> <span data-ttu-id="1f8a3-257">Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-257">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="1f8a3-258">Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-258">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="1f8a3-259">Přístup popsaný v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-259">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="1f8a3-260">Klíče polí jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů (`--0--`, `--1--`, &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-260">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="1f8a3-261">Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-261">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="1f8a3-262">V poli `WriteTo` jsou definovány dva literály objektů, které odrážejí dvě Serilog *jímky*, které popisují umístění pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-262">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="1f8a3-263">Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s použitím dvojité pomlčky (`--`) Notation a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-263">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="1f8a3-264">Key</span><span class="sxs-lookup"><span data-stu-id="1f8a3-264">Key</span></span> | <span data-ttu-id="1f8a3-265">Hodnota</span><span class="sxs-lookup"><span data-stu-id="1f8a3-265">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="1f8a3-266">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="1f8a3-266">Reload secrets</span></span>

<span data-ttu-id="1f8a3-267">Tajné kódy jsou ukládány do mezipaměti, dokud se nevolá `IConfigurationRoot.Reload()`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-267">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="1f8a3-268">Aplikace nedodržuje, zakázala a aktualizovala tajné klíče v trezoru klíčů, dokud se neprovede `Reload`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-268">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="1f8a3-269">Zakázané a neprošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="1f8a3-269">Disabled and expired secrets</span></span>

<span data-ttu-id="1f8a3-270">Zakázané a neplatné tajné klíče vyvolají za běhu `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-270">Disabled and expired secrets throw a `KeyVaultClientException` at runtime.</span></span> <span data-ttu-id="1f8a3-271">Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-271">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1f8a3-272">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="1f8a3-272">Troubleshoot</span></span>

<span data-ttu-id="1f8a3-273">Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-273">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1f8a3-274">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="1f8a3-274">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="1f8a3-275">Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-275">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="1f8a3-276">Trezor klíčů neexistuje v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-276">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="1f8a3-277">Aplikace nemá autorizaci pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-277">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="1f8a3-278">Zásady přístupu nezahrnují oprávnění `Get` a `List`.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-278">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="1f8a3-279">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-279">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="1f8a3-280">Aplikace má nesprávný název trezoru klíčů (`KeyVaultName`), ID aplikace Azure AD (`AzureADApplicationId`) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="1f8a3-280">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="1f8a3-281">Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.</span><span class="sxs-lookup"><span data-stu-id="1f8a3-281">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f8a3-282">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1f8a3-282">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="1f8a3-283">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="1f8a3-283">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="1f8a3-284">Microsoft Azure: dokumentace Key Vault</span><span class="sxs-lookup"><span data-stu-id="1f8a3-284">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="1f8a3-285">Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="1f8a3-285">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="1f8a3-286">KeyVaultClient – třída</span><span class="sxs-lookup"><span data-stu-id="1f8a3-286">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="1f8a3-287">Rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="1f8a3-287">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="1f8a3-288">Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET</span><span class="sxs-lookup"><span data-stu-id="1f8a3-288">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
