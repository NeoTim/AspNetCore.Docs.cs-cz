---
title: Poskytovatel konfigurace Azure Key Vault v ASP.NET Core
author: guardrex
description: Naučte se používat zprostředkovatele konfigurace Azure Key Vault ke konfiguraci aplikace pomocí dvojic název-hodnota načtené za běhu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2019
uid: security/key-vault-configuration
ms.openlocfilehash: cd31094884f53f34d55c0bceabae41ca2bacba4c
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928547"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="be532-103">Poskytovatel konfigurace Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be532-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="be532-104">[Luke Latham](https://github.com/guardrex) a [Andrew Stanton – sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="be532-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="be532-105">Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="be532-106">Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="be532-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="be532-107">Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:</span><span class="sxs-lookup"><span data-stu-id="be532-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="be532-108">Řízení přístupu k citlivým datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be532-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="be532-109">Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="be532-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="be532-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be532-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="be532-111">Balíčky</span><span class="sxs-lookup"><span data-stu-id="be532-111">Packages</span></span>

<span data-ttu-id="be532-112">Přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="be532-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="be532-113">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="be532-113">Sample app</span></span>

<span data-ttu-id="be532-114">Ukázková aplikace běží v jednom ze dvou režimů určených příkazem `#define` v horní části souboru *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="be532-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="be532-115">`Certificate` &ndash; ukazuje použití Azure Key Vaultho ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="be532-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="be532-116">Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be532-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="be532-117">`Managed` &ndash; ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace pro Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="be532-118">Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="be532-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="be532-119">Ukázková verze `Managed` musí být nasazená do Azure.</span><span class="sxs-lookup"><span data-stu-id="be532-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="be532-120">Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="be532-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="be532-121">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru (`#define`), najdete v článku <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="be532-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="be532-122">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="be532-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="be532-123">Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="be532-124">Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="be532-125">Nástroj Správce tajných klíčů vyžaduje vlastnost `<UserSecretsId>` v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="be532-126">Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="be532-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="be532-127">Tajné kódy jsou vytvořeny jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="be532-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="be532-128">Hierarchické hodnoty (konfigurační oddíly) používají jako oddělovač v názvech konfiguračních klíčů v [ASP.NET Core](xref:fundamentals/configuration/index) `:` (dvojtečku).</span><span class="sxs-lookup"><span data-stu-id="be532-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="be532-129">Správce tajného klíče se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="be532-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="be532-130">Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="be532-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="be532-131">Pokud jsou tyto tajné klíče uložené v Azure Key Vault v [tajném úložišti v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílem, `_dev` přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="be532-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="be532-132">Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be532-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="be532-133">Tajné úložiště v produkčním prostředí pomocí Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="be532-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="be532-134">Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="be532-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="be532-135">Další podrobnosti najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="be532-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="be532-136">Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="be532-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="be532-137">Vyberte **vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="be532-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="be532-138">V textovém poli použijte hledaný řetězec "Azure CLI".</span><span class="sxs-lookup"><span data-stu-id="be532-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="be532-139">Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="be532-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="be532-140">V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="be532-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="be532-141">Další informace najdete v tématu [Azure CLI](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="be532-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="be532-142">Pokud jste to ještě neověřili, přihlaste se pomocí příkazu `az login`.</span><span class="sxs-lookup"><span data-stu-id="be532-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="be532-143">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` je oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="be532-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="be532-144">Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` je oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="be532-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="be532-145">Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="be532-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="be532-146">Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="be532-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="be532-147">Hierarchické hodnoty (konfigurační oddíly) používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="be532-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="be532-148">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="be532-149">Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.</span><span class="sxs-lookup"><span data-stu-id="be532-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="be532-150">Následující tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="be532-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="be532-151">Mezi hodnoty patří `_prod` přípona pro odlišení od hodnot `_dev` přípony načtených ve vývojovém prostředí od uživatelských tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="be532-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="be532-152">Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="be532-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="be532-153">Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure</span><span class="sxs-lookup"><span data-stu-id="be532-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="be532-154">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="be532-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="be532-155">Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="be532-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="be532-156">I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="be532-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="be532-157">Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="be532-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="be532-158">Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud je příkaz `#define` v horní části souboru *program.cs* nastavený na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="be532-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="be532-159">Vytvořte certifikát PKCS # 12 ( *. pfx*).</span><span class="sxs-lookup"><span data-stu-id="be532-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="be532-160">Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="be532-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="be532-161">Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="be532-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="be532-162">Označení klíče jako exportovatelných je volitelné.</span><span class="sxs-lookup"><span data-stu-id="be532-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="be532-163">Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="be532-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="be532-164">Exportujte certifikát PKCS # 12 ( *. pfx*) jako certifikát kódovaný v kódování DER ( *. cer*).</span><span class="sxs-lookup"><span data-stu-id="be532-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="be532-165">Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).</span><span class="sxs-lookup"><span data-stu-id="be532-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="be532-166">Nahrajte certifikát s kódováním DER ( *. cer*) do Azure AD:</span><span class="sxs-lookup"><span data-stu-id="be532-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="be532-167">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="be532-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="be532-168">Přejděte na **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="be532-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="be532-169">Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="be532-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="be532-170">Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .</span><span class="sxs-lookup"><span data-stu-id="be532-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="be532-171">V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.</span><span class="sxs-lookup"><span data-stu-id="be532-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="be532-172">V Azure Portal přejděte na **trezory klíčů** .</span><span class="sxs-lookup"><span data-stu-id="be532-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="be532-173">[V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.</span><span class="sxs-lookup"><span data-stu-id="be532-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="be532-174">Vyberte **zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="be532-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="be532-175">Vyberte **Přidat zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="be532-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="be532-176">Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .</span><span class="sxs-lookup"><span data-stu-id="be532-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="be532-177">Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="be532-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="be532-178">Vyberte tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="be532-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="be532-179">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="be532-179">Select **OK**.</span></span>
1. <span data-ttu-id="be532-180">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="be532-180">Select **Save**.</span></span>
1. <span data-ttu-id="be532-181">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="be532-181">Deploy the app.</span></span>

<span data-ttu-id="be532-182">Ukázková aplikace `Certificate` získá své konfigurační hodnoty z `IConfigurationRoot` se stejným názvem jako má tajný název:</span><span class="sxs-lookup"><span data-stu-id="be532-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="be532-183">Hodnoty, které nejsou hierarchicky: hodnota pro `SecretName` je získána pomocí `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="be532-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="be532-184">Hierarchické hodnoty (oddíly): použijte notaci `:` (dvojtečky) nebo metodu rozšíření `GetSection`.</span><span class="sxs-lookup"><span data-stu-id="be532-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="be532-185">K získání hodnoty konfigurace použijte některý z těchto přístupů:</span><span class="sxs-lookup"><span data-stu-id="be532-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="be532-186">Certifikát X. 509 spravuje operační systém.</span><span class="sxs-lookup"><span data-stu-id="be532-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="be532-187">Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> s hodnotami poskytnutými souborem *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="be532-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

<span data-ttu-id="be532-188">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="be532-188">Example values:</span></span>

* <span data-ttu-id="be532-189">Název trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="be532-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="be532-190">ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="be532-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="be532-191">Kryptografický otisk certifikátu: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="be532-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="be532-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="be532-192">*appsettings.json*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

<span data-ttu-id="be532-193">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="be532-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="be532-194">Ve vývojovém prostředí se tajné hodnoty načítají s příponou `_dev`.</span><span class="sxs-lookup"><span data-stu-id="be532-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="be532-195">V produkčním prostředí se hodnoty načítají s příponou `_prod`.</span><span class="sxs-lookup"><span data-stu-id="be532-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="be532-196">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="be532-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="be532-197">**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo nebo tajný klíč klienta) uložené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="be532-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="be532-198">Ukázková aplikace používá spravované identity pro prostředky Azure, pokud je příkaz `#define` v horní části souboru *program.cs* nastavený na `Managed`.</span><span class="sxs-lookup"><span data-stu-id="be532-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="be532-199">Do souboru *appSettings. JSON* aplikace zadejte název trezoru.</span><span class="sxs-lookup"><span data-stu-id="be532-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="be532-200">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená na verzi `Managed`, takže můžete tyto položky konfigurace ignorovat.</span><span class="sxs-lookup"><span data-stu-id="be532-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="be532-201">Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="be532-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="be532-202">Nasaďte ukázkovou aplikaci do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="be532-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="be532-203">Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="be532-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="be532-204">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="be532-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="be532-205">ID objektu se zobrazí v Azure Portal na panelu **identita** App Service.</span><span class="sxs-lookup"><span data-stu-id="be532-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="be532-206">Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="be532-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="be532-207">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="be532-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="be532-208">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="be532-208">The sample app:</span></span>

* <span data-ttu-id="be532-209">Vytvoří instanci třídy `AzureServiceTokenProvider` bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="be532-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="be532-210">Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="be532-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="be532-211">Vytvoří se nový <xref:Microsoft.Azure.KeyVault.KeyVaultClient> s zpětným voláním tokenu instance `AzureServiceTokenProvider`.</span><span class="sxs-lookup"><span data-stu-id="be532-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="be532-212">Instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se používá s výchozí implementací <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, která načte všechny tajné hodnoty a nahradí dvojité spojovníky (`--`) dvojtečkami (`:`) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

<span data-ttu-id="be532-213">Ukázková hodnota názvu trezoru klíčů: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="be532-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="be532-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="be532-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="be532-215">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="be532-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="be532-216">Ve vývojovém prostředí mají tajné hodnoty příponu `_dev`, protože jsou poskytovány uživatelskými tajemstvími.</span><span class="sxs-lookup"><span data-stu-id="be532-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="be532-217">V produkčním prostředí se hodnoty načítají s příponou `_prod`, protože jsou k dispozici v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="be532-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="be532-218">Pokud se zobrazí chyba `Access denied`, potvrďte, že je aplikace zaregistrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="be532-219">Potvrďte, že jste službu restartovali v Azure.</span><span class="sxs-lookup"><span data-stu-id="be532-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="be532-220">Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete v tématu [vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="be532-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configuration-options"></a><span data-ttu-id="be532-221">Možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="be532-221">Configuration options</span></span>

<span data-ttu-id="be532-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> může přijmout <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span><span class="sxs-lookup"><span data-stu-id="be532-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="be532-223">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="be532-223">Property</span></span>         | <span data-ttu-id="be532-224">Popis</span><span class="sxs-lookup"><span data-stu-id="be532-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="be532-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>, který se má použít pro načtení hodnot.</span><span class="sxs-lookup"><span data-stu-id="be532-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="be532-226">instance <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> používaná k řízení načítání tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="be532-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="be532-227">`Timespan` počkat mezi pokusy o cyklické dotazování trezoru klíčů na změny.</span><span class="sxs-lookup"><span data-stu-id="be532-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="be532-228">Výchozí hodnota je `null` (konfigurace není znovu načtena).</span><span class="sxs-lookup"><span data-stu-id="be532-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="be532-229">Identifikátor URI trezoru klíčů</span><span class="sxs-lookup"><span data-stu-id="be532-229">Key vault URI.</span></span> |

::: moniker-end

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="be532-230">Použít předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="be532-230">Use a key name prefix</span></span>

<span data-ttu-id="be532-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> poskytuje přetížení, které přijímá implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, která vám umožní řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="be532-232">Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="be532-233">To vám umožní například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="be532-234">Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="be532-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="be532-235">Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="be532-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="be532-236">V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (v tajných názvech klíčů nejsou povolené tečky).</span><span class="sxs-lookup"><span data-stu-id="be532-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="be532-237">Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="be532-238">V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="be532-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="be532-239">Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret`a vymění verzi při načtení tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="be532-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="be532-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> se volá s vlastním <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span><span class="sxs-lookup"><span data-stu-id="be532-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="be532-241">Implementace <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na předpony verze tajných klíčů, aby se do konfigurace načetl správný tajný kód:</span><span class="sxs-lookup"><span data-stu-id="be532-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="be532-242">`Load` načte tajný klíč, pokud jeho název začíná předponou.</span><span class="sxs-lookup"><span data-stu-id="be532-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="be532-243">Další tajné kódy nejsou načteny.</span><span class="sxs-lookup"><span data-stu-id="be532-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="be532-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="be532-244">`GetKey`:</span></span>
  * <span data-ttu-id="be532-245">Odebere předponu z názvu tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="be532-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="be532-246">Nahradí dvě pomlčky v libovolném názvu `KeyDelimiter`, což je oddělovač použitý v konfiguraci (obvykle dvojtečka).</span><span class="sxs-lookup"><span data-stu-id="be532-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="be532-247">Azure Key Vault v tajných názvech nepovoluje dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="be532-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="be532-248">Metoda `Load` je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="be532-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="be532-249">Pokud je nalezena předpona verze s `Load`, algoritmus používá metodu `GetKey` k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="be532-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="be532-250">Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="be532-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="be532-251">Při implementaci tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="be532-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="be532-252">Verze aplikace zadaná v souboru projektu aplikace</span><span class="sxs-lookup"><span data-stu-id="be532-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="be532-253">V následujícím příkladu je verze aplikace nastavená na `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="be532-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="be532-254">Ověřte, že se v souboru projektu aplikace nachází vlastnost `<UserSecretsId>`, kde `{GUID}` je uživatelem zadaný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="be532-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="be532-255">Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="be532-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="be532-256">Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="be532-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="be532-257">Po spuštění aplikace se načtou tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="be532-258">Řetězec tajného kódu pro `5000-AppSecret` se shoduje s verzí aplikace zadanou v souboru projektu aplikace (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="be532-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="be532-259">Verze, `5000` (s pomlčkou), je z názvu klíče odstraněna.</span><span class="sxs-lookup"><span data-stu-id="be532-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="be532-260">V celé aplikaci se při čtení konfigurace pomocí klíče `AppSecret` načte tajná hodnota.</span><span class="sxs-lookup"><span data-stu-id="be532-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="be532-261">Pokud se verze aplikace v souboru projektu změní na `5.1.0.0` a aplikace se spustí znovu, vrátí se hodnota tajného klíče `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="be532-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="be532-262">Můžete také poskytnout vlastní <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span><span class="sxs-lookup"><span data-stu-id="be532-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="be532-263">Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.</span><span class="sxs-lookup"><span data-stu-id="be532-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="be532-264">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="be532-264">Bind an array to a class</span></span>

<span data-ttu-id="be532-265">Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="be532-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="be532-266">Při čtení ze zdroje konfigurace, který umožňuje klíčům obsahovat oddělovače dvojtečky (`:`), se pro odlišení klíčů, které tvoří pole (`:0:`, `:1:`...) používá číselný segment.</span><span class="sxs-lookup"><span data-stu-id="be532-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="be532-267">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="be532-267">`:{n}:`).</span></span> <span data-ttu-id="be532-268">Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="be532-268">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="be532-269">Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="be532-269">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="be532-270">Přístup popsaný v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="be532-270">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="be532-271">Klíče polí jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů (`--0--`, `--1--`, &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="be532-271">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="be532-272">Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="be532-272">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="be532-273">V poli `WriteTo` jsou definovány dva literály objektů, které odrážejí dvě *jímky*Serilog, které popisují umístění pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="be532-273">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="be532-274">Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s využitím notace dvojité pomlčky (`--`) a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="be532-274">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="be532-275">Key</span><span class="sxs-lookup"><span data-stu-id="be532-275">Key</span></span> | <span data-ttu-id="be532-276">Hodnota</span><span class="sxs-lookup"><span data-stu-id="be532-276">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="be532-277">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="be532-277">Reload secrets</span></span>

<span data-ttu-id="be532-278">Tajné kódy jsou ukládány do mezipaměti, dokud není volána `IConfigurationRoot.Reload()`.</span><span class="sxs-lookup"><span data-stu-id="be532-278">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="be532-279">V případě vypršení platnosti, zakázání a aktualizace tajných klíčů v trezoru klíčů se aplikace nedodržuje, dokud se nespustí `Reload`.</span><span class="sxs-lookup"><span data-stu-id="be532-279">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="be532-280">Zakázané a neprošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="be532-280">Disabled and expired secrets</span></span>

<span data-ttu-id="be532-281">Zakázané a tajné klíče s vypršenou platností vyvolávají <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span><span class="sxs-lookup"><span data-stu-id="be532-281">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="be532-282">Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="be532-282">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="be532-283">Řešení problémů</span><span class="sxs-lookup"><span data-stu-id="be532-283">Troubleshoot</span></span>

<span data-ttu-id="be532-284">Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="be532-284">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="be532-285">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="be532-285">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="be532-286">Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="be532-286">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="be532-287">Trezor klíčů neexistuje v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="be532-287">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="be532-288">Aplikace nemá autorizaci pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="be532-288">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="be532-289">Zásady přístupu nezahrnují oprávnění `Get` a `List`.</span><span class="sxs-lookup"><span data-stu-id="be532-289">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="be532-290">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.</span><span class="sxs-lookup"><span data-stu-id="be532-290">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="be532-291">Aplikace má nesprávný název trezoru klíčů (`KeyVaultName`), ID aplikace Azure AD (`AzureADApplicationId`) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="be532-291">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="be532-292">Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.</span><span class="sxs-lookup"><span data-stu-id="be532-292">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="be532-293">Když přidáte zásady přístupu pro aplikaci do trezoru klíčů, zásada se vytvořila, ale tlačítko **Uložit** nebylo vybrané v uživatelském rozhraní **zásad přístupu** .</span><span class="sxs-lookup"><span data-stu-id="be532-293">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be532-294">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="be532-294">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="be532-295">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="be532-295">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="be532-296">Microsoft Azure: dokumentace Key Vault</span><span class="sxs-lookup"><span data-stu-id="be532-296">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="be532-297">Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="be532-297">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="be532-298">KeyVaultClient – třída</span><span class="sxs-lookup"><span data-stu-id="be532-298">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="be532-299">Rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="be532-299">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="be532-300">Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET</span><span class="sxs-lookup"><span data-stu-id="be532-300">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
