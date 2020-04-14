---
title: Zprostředkovatel konfigurace trezoru klíčů Azure v ASP.NET jádra
author: rick-anderson
description: Zjistěte, jak pomocí zprostředkovatele konfigurace trezoru klíčů Azure nakonfigurovat aplikaci pomocí párů hodnot názvů načtených za běhu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228163"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="2218a-103">Zprostředkovatel konfigurace trezoru klíčů Azure v ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="2218a-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="2218a-104">Podle [Andrew Stanton-Sestra](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="2218a-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2218a-105">Tento dokument vysvětluje, jak pomocí zprostředkovatele konfigurace [úložiště klíčů Microsoft Azure](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z tajných kódů úložiště klíčů Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2218a-106">Azure Key Vault je cloudová služba, která pomáhá při ochraně kryptografických klíčů a tajných kódů používaných aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="2218a-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2218a-107">Mezi běžné scénáře používání služby Azure Key Vault s aplikacemi ASP.NET Core patří:</span><span class="sxs-lookup"><span data-stu-id="2218a-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2218a-108">Řízení přístupu k citlivým konfiguračním datům.</span><span class="sxs-lookup"><span data-stu-id="2218a-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2218a-109">Splnění požadavku na fips 140-2 level 2 ověřené moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="2218a-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2218a-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2218a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2218a-111">Balíčky</span><span class="sxs-lookup"><span data-stu-id="2218a-111">Packages</span></span>

<span data-ttu-id="2218a-112">Přidejte odkaz na balíček na balíček [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="2218a-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2218a-113">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="2218a-113">Sample app</span></span>

<span data-ttu-id="2218a-114">Ukázková aplikace běží v jednom ze `#define` dvou režimů určených příkazem v horní části *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="2218a-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2218a-115">`Certificate`&ndash; Ukazuje použití ID klienta trezoru klíčů Azure a certifikátu X.509 pro přístup k tajným kódům uloženým v úložišti klíčů Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2218a-116">Tuto verzi ukázky lze spustit z libovolného umístění, nasazené do služby Azure App Service nebo libovolného hostitele schopného obsluhovat aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2218a-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2218a-117">`Managed`&ndash; Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2218a-118">Při použití spravovaných identit k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="2218a-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2218a-119">Verze `Managed` ukázky musí být nasazená do Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2218a-120">Postupujte podle pokynů v části [Použití spravovaných identit pro prostředky Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="2218a-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2218a-121">Další informace o konfiguraci ukázkové aplikace pomocí`#define`direktiv preprocesoru ( ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="2218a-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2218a-122">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="2218a-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="2218a-123">Nastavte tajné klíče místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2218a-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2218a-124">Když ukázková aplikace běží v místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="2218a-125">Nástroj Správce tajných `<UserSecretsId>` barev vyžaduje vlastnost v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2218a-126">Nastavte hodnotu`{GUID}`vlastnosti ( ) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="2218a-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2218a-127">Tajné klíče jsou vytvořeny jako dvojice název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="2218a-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2218a-128">Hierarchické hodnoty (konfigurační oddíly) používají `:` (dvojtečku) jako oddělovač v názvech ASP.NET [konfiguračních klíčů Jádra.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="2218a-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2218a-129">Správce tajných barev se používá z příkazového prostředí otevřeného do `{SECRET VALUE}` [kořenového adresáře obsahu](xref:fundamentals/index#content-root)projektu , kde `{SECRET NAME}` je název a je hodnota:</span><span class="sxs-lookup"><span data-stu-id="2218a-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2218a-130">Spusťte následující příkazy v příkazovém prostředí z [kořenového adresáře obsahu](xref:fundamentals/index#content-root) projektu a nastavte tajné kódy ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="2218a-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2218a-131">Když jsou tyto tajné klíče uloženy v trezoru klíčů Azure v `_dev` [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault, přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="2218a-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2218a-132">Přípona poskytuje vizuální podnět ve výstupu aplikace označující zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2218a-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2218a-133">Tajné úložiště v produkčním prostředí s trezorem klíčů Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2218a-134">Pokyny poskytnuté na [úvodním panelu: Nastavení a načtení tajného klíče z azure key vault pomocí tématu Azure CLI](/azure/key-vault/quick-create-cli) jsou shrnuty zde pro vytvoření trezoru klíčů Azure a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="2218a-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2218a-135">Další podrobnosti naleznete v tématu.</span><span class="sxs-lookup"><span data-stu-id="2218a-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2218a-136">Otevřete prostředí Azure Cloud pomocí některé z následujících metod na [webu Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="2218a-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2218a-137">Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="2218a-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2218a-138">Použijte vyhledávací řetězec "Azure CLI" v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="2218a-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2218a-139">Otevřete Cloud Shell ve svém prohlížeči pomocí tlačítka **Spustit cloudové prostředí.**</span><span class="sxs-lookup"><span data-stu-id="2218a-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2218a-140">V nabídce v pravém horním rohu portálu Azure vyberte tlačítko **Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="2218a-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2218a-141">Další informace najdete v [tématu Azure CLI](/cli/azure/) a [přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="2218a-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2218a-142">Pokud ještě nejste ověřeni, přihlaste se pomocí příkazu. `az login`</span><span class="sxs-lookup"><span data-stu-id="2218a-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2218a-143">Vytvořte skupinu prostředků s `{RESOURCE GROUP NAME}` následujícím příkazem, kde je název `{LOCATION}` skupiny prostředků pro novou skupinu prostředků a je oblast Azure (datové centrum):</span><span class="sxs-lookup"><span data-stu-id="2218a-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2218a-144">Vytvořte trezor klíčů ve skupině prostředků pomocí `{KEY VAULT NAME}` následujícího příkazu, kde `{LOCATION}` je název pro nový trezor klíčů a je oblast Azure (datové centrum):</span><span class="sxs-lookup"><span data-stu-id="2218a-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2218a-145">Vytvořte tajné klíče v trezoru klíčů jako dvojice název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="2218a-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2218a-146">Tajné názvy azure key vault jsou omezeny na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="2218a-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2218a-147">Hierarchické hodnoty (konfigurační sekce) používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="2218a-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2218a-148">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET jádra](xref:fundamentals/configuration/index), nejsou v tajných názvech trezoru klíčů povoleny.</span><span class="sxs-lookup"><span data-stu-id="2218a-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2218a-149">Proto dvě pomlčky se používají a prohozeny pro dvojtečku při načtení tajných kódů do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2218a-150">Následující tajné klíče jsou určeny pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="2218a-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2218a-151">Hodnoty zahrnují `_prod` příponu k jejich `_dev` odlišení od hodnot přípony načtených ve vývojovém prostředí od tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2218a-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2218a-152">Nahraďte `{KEY VAULT NAME}` název trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="2218a-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2218a-153">Použití ID aplikace a certifikátu X.509 pro aplikace, které nejsou hostované v Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2218a-154">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používaly ID aplikace Azure Active Directory a certifikát X.509 k ověření do trezoru **klíčů, když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="2218a-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="2218a-155">Další informace naleznete v [tématu O klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="2218a-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2218a-156">I když je pro aplikace hostované v Azure podporované id aplikace a certifikát X.509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="2218a-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2218a-157">Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2218a-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2218a-158">Ukázková aplikace používá id aplikace a certifikát X.509, `#define` pokud je příkaz v horní části *Program.cs* soubornastaven na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="2218a-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2218a-159">Vytvořte certifikát archivu PKCS#12 (*.pfx).*</span><span class="sxs-lookup"><span data-stu-id="2218a-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="2218a-160">Mezi možnosti vytváření certifikátů patří [MakeCert v systémech Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="2218a-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2218a-161">Nainstalujte certifikát do úložiště osobních certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="2218a-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2218a-162">Označení klíče jako exportovatelného je volitelné.</span><span class="sxs-lookup"><span data-stu-id="2218a-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2218a-163">Všimněte si kryptografického otisku certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="2218a-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2218a-164">Exportujte certifikát Archivu PKCS#12 (*.pfx)* jako certifikát kódovaný der (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="2218a-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="2218a-165">Zaregistrujte aplikaci pomocí Azure AD **(Registrace aplikací).**</span><span class="sxs-lookup"><span data-stu-id="2218a-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="2218a-166">Nahrajte certifikát kódovaný der (*.cer*) do Služby Azure AD:</span><span class="sxs-lookup"><span data-stu-id="2218a-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="2218a-167">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2218a-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2218a-168">Přejděte na **tajné klíče & certifikáty**.</span><span class="sxs-lookup"><span data-stu-id="2218a-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="2218a-169">Výběrem **možnosti Odeslat certifikát** nahrajete certifikát, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="2218a-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2218a-170">Certifikát *.cer*, *.pem*nebo *.crt* je přijatelný.</span><span class="sxs-lookup"><span data-stu-id="2218a-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2218a-171">Uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu do souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2218a-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="2218a-172">Přejděte do **trezorů klíčů** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2218a-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2218a-173">Vyberte trezor klíčů, který jste vytvořili v [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2218a-174">Vyberte **Zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="2218a-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="2218a-175">Vyberte **Přidat zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="2218a-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="2218a-176">Otevřete **oprávnění Tajné a** poskytněte aplikaci oprávnění **Získat** a **Seznam.**</span><span class="sxs-lookup"><span data-stu-id="2218a-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2218a-177">Vyberte **Vybrat hlavní objekt** a vyberte registrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2218a-178">Vyberte tlačítko **Vybrat.**</span><span class="sxs-lookup"><span data-stu-id="2218a-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="2218a-179">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2218a-179">Select **OK**.</span></span>
1. <span data-ttu-id="2218a-180">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2218a-180">Select **Save**.</span></span>
1. <span data-ttu-id="2218a-181">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2218a-181">Deploy the app.</span></span>

<span data-ttu-id="2218a-182">Ukázková `Certificate` aplikace získává své `IConfigurationRoot` hodnoty konfigurace ze stejného názvu jako tajný název:</span><span class="sxs-lookup"><span data-stu-id="2218a-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2218a-183">Nehierarchické hodnoty: Hodnota `SecretName` pro je `config["SecretName"]`získána pomocí .</span><span class="sxs-lookup"><span data-stu-id="2218a-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2218a-184">Hierarchické hodnoty (oddíly): Použijte `:` zápis `GetSection` (dvojtečka) nebo metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="2218a-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2218a-185">Pomocí některého z těchto přístupů získáte hodnotu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2218a-186">Certifikát X.509 je spravován os.</span><span class="sxs-lookup"><span data-stu-id="2218a-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2218a-187">Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> s hodnotami dodanými souborem *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2218a-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="2218a-188">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="2218a-188">Example values:</span></span>

* <span data-ttu-id="2218a-189">Název trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="2218a-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2218a-190">ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2218a-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2218a-191">Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2218a-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2218a-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2218a-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="2218a-193">Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2218a-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2218a-194">Ve vývojovém prostředí se tajné `_dev` hodnoty načítají s příponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2218a-195">V produkčním prostředí se hodnoty `_prod` načítají s příponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2218a-196">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2218a-197">**Aplikace nasazená do Azure** může využívat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje aplikaci ověřovat pomocí Azure Key Vault pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a tajný klíč klienta) uložené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2218a-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2218a-198">Ukázková aplikace používá spravované identity `#define` pro prostředky Azure, když je `Managed`příkaz v horní části *Program.cs* souboru nastavenna .</span><span class="sxs-lookup"><span data-stu-id="2218a-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2218a-199">Zadejte název úschovny do souboru *appsettings.json* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="2218a-200">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč `Managed` klienta) při nastavení na verzi, takže můžete tyto položky konfigurace ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2218a-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2218a-201">Aplikace se nasadí do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2218a-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="2218a-202">Nasaďte ukázkovou aplikaci do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2218a-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2218a-203">Aplikace nasazená ve službě Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2218a-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2218a-204">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="2218a-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2218a-205">ID objektu se zobrazí na portálu Azure na panelu **Identita služby** App Service.</span><span class="sxs-lookup"><span data-stu-id="2218a-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="2218a-206">Pomocí Azure CLI a ID objektu aplikace, `list` `get` poskytnout aplikaci a oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="2218a-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2218a-207">**Restartujte aplikaci** pomocí Azure CLI, PowerShellu nebo portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2218a-208">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="2218a-208">The sample app:</span></span>

* <span data-ttu-id="2218a-209">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="2218a-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="2218a-210">Když není k dispozici připojovací řetězec, poskytovatel se pokusí získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="2218a-211">Nový <xref:Microsoft.Azure.KeyVault.KeyVaultClient> je vytvořen `AzureServiceTokenProvider` s token instance zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="2218a-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="2218a-212">Instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se používá s výchozí <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementací, která načte všechny`--`tajné hodnoty a`:`nahradí dvojité pomlčky ( ) dvojtečkami ( ) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="2218a-213">Příklad příklad názvu trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="2218a-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="2218a-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2218a-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2218a-215">Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2218a-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2218a-216">Ve vývojovém prostředí mají `_dev` tajné hodnoty příponu, protože jsou poskytovány tajnými kódy uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2218a-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2218a-217">V produkčním prostředí se hodnoty `_prod` načítají s příponou, protože jsou poskytovány službou Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2218a-218">Pokud se `Access denied` zobrazí chyba, zkontrolujte, že aplikace je registrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2218a-219">Zkontrolujte, že jste službu v Azure restartovali.</span><span class="sxs-lookup"><span data-stu-id="2218a-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2218a-220">Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete [v tématu Vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="2218a-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="2218a-221">Možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="2218a-221">Configuration options</span></span>

<span data-ttu-id="2218a-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>může přijmout <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span><span class="sxs-lookup"><span data-stu-id="2218a-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="2218a-223">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="2218a-223">Property</span></span>         | <span data-ttu-id="2218a-224">Popis</span><span class="sxs-lookup"><span data-stu-id="2218a-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="2218a-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>pro načítání hodnot.</span><span class="sxs-lookup"><span data-stu-id="2218a-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="2218a-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instance slouží k řízení tajnénačítání.</span><span class="sxs-lookup"><span data-stu-id="2218a-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="2218a-227">`Timespan`čekání mezi pokusy o dotazování trezoru klíčů pro změny.</span><span class="sxs-lookup"><span data-stu-id="2218a-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="2218a-228">Výchozí hodnota `null` je (konfigurace není znovu načtena).</span><span class="sxs-lookup"><span data-stu-id="2218a-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="2218a-229">Identifikátor URI trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2218a-230">Použití předpony názvu klíče</span><span class="sxs-lookup"><span data-stu-id="2218a-230">Use a key name prefix</span></span>

<span data-ttu-id="2218a-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementaci aplikace , která umožňuje řídit, jak jsou tajné klíče trezoru klíčů převedeny na konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="2218a-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2218a-232">Můžete například implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2218a-233">To umožňuje například načíst tajné klíče na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2218a-234">Nepoužívejte předpony na tajných kódech trezoru klíčů k umístění tajných kódů pro více aplikací do stejného trezoru klíčů nebo k umístění environmentálních tajemství (například *ukvačování vývoje* versus *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="2218a-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2218a-235">Doporučujeme, aby různé aplikace a vývojová a produkční prostředí používaly samostatné trezory klíčů k oddělení prostředí aplikací pro nejvyšší úroveň zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2218a-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2218a-236">V následujícím příkladu je v trezoru klíčů vytvořen tajný klíč (a použití `5000-AppSecret` nástroje Správce tajných klíčů pro vývojové prostředí) (období nejsou povolena v názvech tajných klíčů).</span><span class="sxs-lookup"><span data-stu-id="2218a-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2218a-237">Tento tajný klíč představuje tajný klíč aplikace pro verzi 5.0.0.0 aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2218a-238">Pro jinou verzi aplikace, 5.1.0.0, je tajný klíč přidán do trezoru `5100-AppSecret`klíčů (a pomocí nástroje Správce tajných klíčů) pro .</span><span class="sxs-lookup"><span data-stu-id="2218a-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2218a-239">Každá verze aplikace načte svou verzi `AppSecret`tajné hodnoty do své konfigurace jako , stripping off verzi, jak se načte tajný klíč.</span><span class="sxs-lookup"><span data-stu-id="2218a-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2218a-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se nazývá s <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>vlastní :</span><span class="sxs-lookup"><span data-stu-id="2218a-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2218a-241">Implementace <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na předpony verzí tajných kódů k načtení správného tajného klíče do konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2218a-242">`Load`načte tajný klíč, když jeho název začíná předponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2218a-243">Ostatní tajemství nejsou nabitá.</span><span class="sxs-lookup"><span data-stu-id="2218a-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2218a-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2218a-244">`GetKey`:</span></span>
  * <span data-ttu-id="2218a-245">Odebere předponu z tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2218a-246">Nahradí dvě pomlčky v libovolném názvu `KeyDelimiter`s , což je oddělovač používaný v konfiguraci (obvykle dvojtečka).</span><span class="sxs-lookup"><span data-stu-id="2218a-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2218a-247">Azure Key Vault neumožňuje dvojtečku v tajných názvech.</span><span class="sxs-lookup"><span data-stu-id="2218a-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2218a-248">Metoda `Load` je volána algoritmus zprostředkovatele, který itepraví přes tajné kódy trezoru najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="2218a-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2218a-249">Pokud je nalezena předpona verze `Load` `GetKey` s , algoritmus používá metodu k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2218a-250">Odstraní předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název názvu konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2218a-251">Při provádění tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="2218a-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="2218a-252">Verze aplikace zadaná v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2218a-253">V následujícím příkladu je verze aplikace `5.0.0.0`nastavena na :</span><span class="sxs-lookup"><span data-stu-id="2218a-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2218a-254">Zkontrolujte, `<UserSecretsId>` zda je v souboru projektu `{GUID}` aplikace k dispozici vlastnost, kde je identifikátor GUID dodaný uživatelem:</span><span class="sxs-lookup"><span data-stu-id="2218a-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2218a-255">Uložte následující tajné kódy místně pomocí [nástroje Správce tajných barev](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="2218a-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2218a-256">Tajné klíče se ukládají do úložiště klíčů Azure pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="2218a-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2218a-257">Při spuštění aplikace jsou načteny tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2218a-258">Tajný klíč `5000-AppSecret` řetězce pro je spárován s verzí aplikace zadanou v`5.0.0.0`souboru projektu aplikace ( ).</span><span class="sxs-lookup"><span data-stu-id="2218a-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2218a-259">Verze `5000` (s pomlčkou) je odstraněna z názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="2218a-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2218a-260">V celé aplikaci čtení `AppSecret` konfigurace s klíčem načte tajnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2218a-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2218a-261">Pokud se verze aplikace změní v souboru projektu a `5.1.0.0` aplikace se spustí `5.1.0.0_secret_value_dev` znovu, vrácená `5.1.0.0_secret_value_prod` tajná hodnota je ve vývojovém prostředí a v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="2218a-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2218a-262">Můžete také poskytnout <xref:Microsoft.Azure.KeyVault.KeyVaultClient> vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementaci .</span><span class="sxs-lookup"><span data-stu-id="2218a-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="2218a-263">Vlastní klient umožňuje sdílení jedné instance klienta v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2218a-264">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="2218a-264">Bind an array to a class</span></span>

<span data-ttu-id="2218a-265">Zprostředkovatel je schopen číst hodnoty konfigurace do pole pro vazbu na pole POCO.</span><span class="sxs-lookup"><span data-stu-id="2218a-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2218a-266">Při čtení ze zdroje konfigurace, který`:`umožňuje klíčům obsahovat oddělovače dvojtečky ( ),`:0:`se `:1:` &hellip; `:{n}:`k rozlišení klíčů tvořících pole používá číselný segment klíče ( , , ).</span><span class="sxs-lookup"><span data-stu-id="2218a-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2218a-267">Další informace naleznete v [tématu Konfigurace: Vazba pole na třídu](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2218a-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2218a-268">Klíče Azure Key Vault nelze použít dvojtečku jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="2218a-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2218a-269">Přístup popsaný v tomto tématu používá`--`dvojité pomlčky ( ) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="2218a-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2218a-270">Klíče pole jsou uloženy v trezoru klíčů Azure`--0--`s `--1--` &hellip; `--{n}--`dvojitými pomlčkami a číselnými segmenty klíčů ( , , ).</span><span class="sxs-lookup"><span data-stu-id="2218a-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2218a-271">Prohlédněte si následující konfiguraci zprostředkovatele protokolování [Serilog](https://serilog.net/) poskytovou souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="2218a-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2218a-272">V `WriteTo` poli jsou definovány dva literály objektu, které odrážejí dvě *jímky*Serilog , které popisují cíle pro protokolování výstupu:</span><span class="sxs-lookup"><span data-stu-id="2218a-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="2218a-273">Konfigurace zobrazená v předchozím souboru JSON je uložena`--`v azure key vaultu pomocí dvojité pomlčky ( ) zápisu a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="2218a-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2218a-274">Klíč</span><span class="sxs-lookup"><span data-stu-id="2218a-274">Key</span></span> | <span data-ttu-id="2218a-275">Hodnota</span><span class="sxs-lookup"><span data-stu-id="2218a-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2218a-276">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="2218a-276">Reload secrets</span></span>

<span data-ttu-id="2218a-277">Tajné klíče jsou `IConfigurationRoot.Reload()` ukládány do mezipaměti, dokud není volána.</span><span class="sxs-lookup"><span data-stu-id="2218a-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2218a-278">Vypršela platnost, zakázáno a aktualizované tajné klíče v trezoru klíčů nejsou respektovány aplikací, dokud `Reload` není proveden.</span><span class="sxs-lookup"><span data-stu-id="2218a-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2218a-279">Zakázané a prošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="2218a-279">Disabled and expired secrets</span></span>

<span data-ttu-id="2218a-280">Zakázané a vypršela <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>platnost tajných kódů hodit .</span><span class="sxs-lookup"><span data-stu-id="2218a-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="2218a-281">Chcete-li zabránit vyvolání aplikace, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázaný tajný klíč nebo tajný klíč, jehož platnost vypršela.</span><span class="sxs-lookup"><span data-stu-id="2218a-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2218a-282">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2218a-282">Troubleshoot</span></span>

<span data-ttu-id="2218a-283">Když se aplikaci nepodaří načíst konfiguraci pomocí zprostředkovatele, zapíše se chybová zpráva do [infrastruktury ASP.NET core logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2218a-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2218a-284">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2218a-285">Aplikace nebo certifikát není správně nakonfigurovaný ve službě Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2218a-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2218a-286">Trezor klíčů neexistuje v azure key vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2218a-287">Aplikace nemá oprávnění k přístupu do trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2218a-288">Zásady přístupu `Get` neobsahuje `List` a oprávnění.</span><span class="sxs-lookup"><span data-stu-id="2218a-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2218a-289">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela jejich platnost.</span><span class="sxs-lookup"><span data-stu-id="2218a-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2218a-290">Aplikace má nesprávný název`KeyVaultName`trezoru klíčů ( ),`AzureADApplicationId`ID aplikace Azure AD ( ) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="2218a-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="2218a-291">Konfigurační klíč (název) je v aplikaci nesprávný pro hodnotu, kterou se pokoušíte načíst.</span><span class="sxs-lookup"><span data-stu-id="2218a-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2218a-292">Při přidávání zásad přístupu pro aplikaci do trezoru klíčů byla zásada vytvořena, ale tlačítko **Uložit** nebylo v umělou iniciálním nastavení **zásad přístupu** vybráno.</span><span class="sxs-lookup"><span data-stu-id="2218a-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2218a-293">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2218a-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2218a-294">Microsoft Azure: Trezor klíčů</span><span class="sxs-lookup"><span data-stu-id="2218a-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2218a-295">Microsoft Azure: Dokumentace trezoru klíčů</span><span class="sxs-lookup"><span data-stu-id="2218a-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2218a-296">Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2218a-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2218a-297">Třída KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="2218a-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2218a-298">Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="2218a-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2218a-299">Kurz: Jak používat Azure Key Vault s Azure Windows Virtual Machine v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="2218a-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2218a-300">Tento dokument vysvětluje, jak pomocí zprostředkovatele konfigurace [úložiště klíčů Microsoft Azure](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z tajných kódů úložiště klíčů Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="2218a-301">Azure Key Vault je cloudová služba, která pomáhá při ochraně kryptografických klíčů a tajných kódů používaných aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="2218a-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="2218a-302">Mezi běžné scénáře používání služby Azure Key Vault s aplikacemi ASP.NET Core patří:</span><span class="sxs-lookup"><span data-stu-id="2218a-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="2218a-303">Řízení přístupu k citlivým konfiguračním datům.</span><span class="sxs-lookup"><span data-stu-id="2218a-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="2218a-304">Splnění požadavku na fips 140-2 level 2 ověřené moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="2218a-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="2218a-305">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2218a-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="2218a-306">Balíčky</span><span class="sxs-lookup"><span data-stu-id="2218a-306">Packages</span></span>

<span data-ttu-id="2218a-307">Přidejte odkaz na balíček na balíček [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="2218a-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="2218a-308">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="2218a-308">Sample app</span></span>

<span data-ttu-id="2218a-309">Ukázková aplikace běží v jednom ze `#define` dvou režimů určených příkazem v horní části *Program.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="2218a-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="2218a-310">`Certificate`&ndash; Ukazuje použití ID klienta trezoru klíčů Azure a certifikátu X.509 pro přístup k tajným kódům uloženým v úložišti klíčů Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="2218a-311">Tuto verzi ukázky lze spustit z libovolného umístění, nasazené do služby Azure App Service nebo libovolného hostitele schopného obsluhovat aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2218a-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="2218a-312">`Managed`&ndash; Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="2218a-313">Při použití spravovaných identit k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="2218a-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="2218a-314">Verze `Managed` ukázky musí být nasazená do Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="2218a-315">Postupujte podle pokynů v části [Použití spravovaných identit pro prostředky Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="2218a-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="2218a-316">Další informace o konfiguraci ukázkové aplikace pomocí`#define`direktiv preprocesoru ( ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="2218a-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="2218a-317">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="2218a-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="2218a-318">Nastavte tajné klíče místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2218a-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2218a-319">Když ukázková aplikace běží v místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště Správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="2218a-320">Nástroj Správce tajných `<UserSecretsId>` barev vyžaduje vlastnost v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="2218a-321">Nastavte hodnotu`{GUID}`vlastnosti ( ) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="2218a-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="2218a-322">Tajné klíče jsou vytvořeny jako dvojice název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="2218a-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="2218a-323">Hierarchické hodnoty (konfigurační oddíly) používají `:` (dvojtečku) jako oddělovač v názvech ASP.NET [konfiguračních klíčů Jádra.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="2218a-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="2218a-324">Správce tajných barev se používá z příkazového prostředí otevřeného do `{SECRET VALUE}` [kořenového adresáře obsahu](xref:fundamentals/index#content-root)projektu , kde `{SECRET NAME}` je název a je hodnota:</span><span class="sxs-lookup"><span data-stu-id="2218a-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="2218a-325">Spusťte následující příkazy v příkazovém prostředí z [kořenového adresáře obsahu](xref:fundamentals/index#content-root) projektu a nastavte tajné kódy ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="2218a-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="2218a-326">Když jsou tyto tajné klíče uloženy v trezoru klíčů Azure v `_dev` [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault, přípona se změní na `_prod`.</span><span class="sxs-lookup"><span data-stu-id="2218a-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="2218a-327">Přípona poskytuje vizuální podnět ve výstupu aplikace označující zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2218a-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="2218a-328">Tajné úložiště v produkčním prostředí s trezorem klíčů Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="2218a-329">Pokyny poskytnuté na [úvodním panelu: Nastavení a načtení tajného klíče z azure key vault pomocí tématu Azure CLI](/azure/key-vault/quick-create-cli) jsou shrnuty zde pro vytvoření trezoru klíčů Azure a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="2218a-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="2218a-330">Další podrobnosti naleznete v tématu.</span><span class="sxs-lookup"><span data-stu-id="2218a-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="2218a-331">Otevřete prostředí Azure Cloud pomocí některé z následujících metod na [webu Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="2218a-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="2218a-332">Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="2218a-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="2218a-333">Použijte vyhledávací řetězec "Azure CLI" v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="2218a-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="2218a-334">Otevřete Cloud Shell ve svém prohlížeči pomocí tlačítka **Spustit cloudové prostředí.**</span><span class="sxs-lookup"><span data-stu-id="2218a-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="2218a-335">V nabídce v pravém horním rohu portálu Azure vyberte tlačítko **Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="2218a-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="2218a-336">Další informace najdete v [tématu Azure CLI](/cli/azure/) a [přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="2218a-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="2218a-337">Pokud ještě nejste ověřeni, přihlaste se pomocí příkazu. `az login`</span><span class="sxs-lookup"><span data-stu-id="2218a-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="2218a-338">Vytvořte skupinu prostředků s `{RESOURCE GROUP NAME}` následujícím příkazem, kde je název `{LOCATION}` skupiny prostředků pro novou skupinu prostředků a je oblast Azure (datové centrum):</span><span class="sxs-lookup"><span data-stu-id="2218a-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2218a-339">Vytvořte trezor klíčů ve skupině prostředků pomocí `{KEY VAULT NAME}` následujícího příkazu, kde `{LOCATION}` je název pro nový trezor klíčů a je oblast Azure (datové centrum):</span><span class="sxs-lookup"><span data-stu-id="2218a-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="2218a-340">Vytvořte tajné klíče v trezoru klíčů jako dvojice název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="2218a-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="2218a-341">Tajné názvy azure key vault jsou omezeny na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="2218a-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="2218a-342">Hierarchické hodnoty (konfigurační sekce) používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="2218a-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2218a-343">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET jádra](xref:fundamentals/configuration/index), nejsou v tajných názvech trezoru klíčů povoleny.</span><span class="sxs-lookup"><span data-stu-id="2218a-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="2218a-344">Proto dvě pomlčky se používají a prohozeny pro dvojtečku při načtení tajných kódů do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="2218a-345">Následující tajné klíče jsou určeny pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="2218a-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="2218a-346">Hodnoty zahrnují `_prod` příponu k jejich `_dev` odlišení od hodnot přípony načtených ve vývojovém prostředí od tajných kódů uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2218a-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="2218a-347">Nahraďte `{KEY VAULT NAME}` název trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="2218a-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="2218a-348">Použití ID aplikace a certifikátu X.509 pro aplikace, které nejsou hostované v Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="2218a-349">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používaly ID aplikace Azure Active Directory a certifikát X.509 k ověření do trezoru **klíčů, když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="2218a-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="2218a-350">Další informace naleznete v [tématu O klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="2218a-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="2218a-351">I když je pro aplikace hostované v Azure podporované id aplikace a certifikát X.509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="2218a-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="2218a-352">Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2218a-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="2218a-353">Ukázková aplikace používá id aplikace a certifikát X.509, `#define` pokud je příkaz v horní části *Program.cs* soubornastaven na `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="2218a-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="2218a-354">Vytvořte certifikát archivu PKCS#12 (*.pfx).*</span><span class="sxs-lookup"><span data-stu-id="2218a-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="2218a-355">Mezi možnosti vytváření certifikátů patří [MakeCert v systémech Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="2218a-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="2218a-356">Nainstalujte certifikát do úložiště osobních certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="2218a-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="2218a-357">Označení klíče jako exportovatelného je volitelné.</span><span class="sxs-lookup"><span data-stu-id="2218a-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="2218a-358">Všimněte si kryptografického otisku certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="2218a-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="2218a-359">Exportujte certifikát Archivu PKCS#12 (*.pfx)* jako certifikát kódovaný der (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="2218a-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="2218a-360">Zaregistrujte aplikaci pomocí Azure AD **(Registrace aplikací).**</span><span class="sxs-lookup"><span data-stu-id="2218a-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="2218a-361">Nahrajte certifikát kódovaný der (*.cer*) do Služby Azure AD:</span><span class="sxs-lookup"><span data-stu-id="2218a-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="2218a-362">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2218a-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="2218a-363">Přejděte na **tajné klíče & certifikáty**.</span><span class="sxs-lookup"><span data-stu-id="2218a-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="2218a-364">Výběrem **možnosti Odeslat certifikát** nahrajete certifikát, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="2218a-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="2218a-365">Certifikát *.cer*, *.pem*nebo *.crt* je přijatelný.</span><span class="sxs-lookup"><span data-stu-id="2218a-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="2218a-366">Uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu do souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2218a-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="2218a-367">Přejděte do **trezorů klíčů** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="2218a-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="2218a-368">Vyberte trezor klíčů, který jste vytvořili v [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="2218a-369">Vyberte **Zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="2218a-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="2218a-370">Vyberte **Přidat zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="2218a-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="2218a-371">Otevřete **oprávnění Tajné a** poskytněte aplikaci oprávnění **Získat** a **Seznam.**</span><span class="sxs-lookup"><span data-stu-id="2218a-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="2218a-372">Vyberte **Vybrat hlavní objekt** a vyberte registrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="2218a-373">Vyberte tlačítko **Vybrat.**</span><span class="sxs-lookup"><span data-stu-id="2218a-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="2218a-374">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="2218a-374">Select **OK**.</span></span>
1. <span data-ttu-id="2218a-375">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="2218a-375">Select **Save**.</span></span>
1. <span data-ttu-id="2218a-376">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2218a-376">Deploy the app.</span></span>

<span data-ttu-id="2218a-377">Ukázková `Certificate` aplikace získává své `IConfigurationRoot` hodnoty konfigurace ze stejného názvu jako tajný název:</span><span class="sxs-lookup"><span data-stu-id="2218a-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="2218a-378">Nehierarchické hodnoty: Hodnota `SecretName` pro je `config["SecretName"]`získána pomocí .</span><span class="sxs-lookup"><span data-stu-id="2218a-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="2218a-379">Hierarchické hodnoty (oddíly): Použijte `:` zápis `GetSection` (dvojtečka) nebo metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="2218a-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="2218a-380">Pomocí některého z těchto přístupů získáte hodnotu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="2218a-381">Certifikát X.509 je spravován os.</span><span class="sxs-lookup"><span data-stu-id="2218a-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="2218a-382">Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> s hodnotami dodanými souborem *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="2218a-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="2218a-383">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="2218a-383">Example values:</span></span>

* <span data-ttu-id="2218a-384">Název trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="2218a-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="2218a-385">ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="2218a-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="2218a-386">Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="2218a-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="2218a-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2218a-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="2218a-388">Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2218a-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2218a-389">Ve vývojovém prostředí se tajné `_dev` hodnoty načítají s příponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="2218a-390">V produkčním prostředí se hodnoty `_prod` načítají s příponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="2218a-391">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="2218a-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="2218a-392">**Aplikace nasazená do Azure** může využívat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje aplikaci ověřovat pomocí Azure Key Vault pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a tajný klíč klienta) uložené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2218a-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="2218a-393">Ukázková aplikace používá spravované identity `#define` pro prostředky Azure, když je `Managed`příkaz v horní části *Program.cs* souboru nastavenna .</span><span class="sxs-lookup"><span data-stu-id="2218a-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="2218a-394">Zadejte název úschovny do souboru *appsettings.json* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="2218a-395">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč `Managed` klienta) při nastavení na verzi, takže můžete tyto položky konfigurace ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2218a-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="2218a-396">Aplikace se nasadí do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2218a-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="2218a-397">Nasaďte ukázkovou aplikaci do služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2218a-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="2218a-398">Aplikace nasazená ve službě Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2218a-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="2218a-399">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="2218a-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="2218a-400">ID objektu se zobrazí na portálu Azure na panelu **Identita služby** App Service.</span><span class="sxs-lookup"><span data-stu-id="2218a-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="2218a-401">Pomocí Azure CLI a ID objektu aplikace, `list` `get` poskytnout aplikaci a oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="2218a-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="2218a-402">**Restartujte aplikaci** pomocí Azure CLI, PowerShellu nebo portálu Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="2218a-403">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="2218a-403">The sample app:</span></span>

* <span data-ttu-id="2218a-404">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="2218a-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="2218a-405">Když není k dispozici připojovací řetězec, poskytovatel se pokusí získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="2218a-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="2218a-406">Nový <xref:Microsoft.Azure.KeyVault.KeyVaultClient> je vytvořen `AzureServiceTokenProvider` s token instance zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="2218a-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="2218a-407">Instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se používá s výchozí <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementací, která načte všechny`--`tajné hodnoty a`:`nahradí dvojité pomlčky ( ) dvojtečkami ( ) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="2218a-408">Příklad příklad názvu trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="2218a-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="2218a-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2218a-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="2218a-410">Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2218a-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="2218a-411">Ve vývojovém prostředí mají `_dev` tajné hodnoty příponu, protože jsou poskytovány tajnými kódy uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2218a-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="2218a-412">V produkčním prostředí se hodnoty `_prod` načítají s příponou, protože jsou poskytovány službou Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="2218a-413">Pokud se `Access denied` zobrazí chyba, zkontrolujte, že aplikace je registrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="2218a-414">Zkontrolujte, že jste službu v Azure restartovali.</span><span class="sxs-lookup"><span data-stu-id="2218a-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="2218a-415">Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete [v tématu Vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="2218a-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="2218a-416">Použití předpony názvu klíče</span><span class="sxs-lookup"><span data-stu-id="2218a-416">Use a key name prefix</span></span>

<span data-ttu-id="2218a-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementaci aplikace , která umožňuje řídit, jak jsou tajné klíče trezoru klíčů převedeny na konfigurační klíče.</span><span class="sxs-lookup"><span data-stu-id="2218a-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="2218a-418">Můžete například implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="2218a-419">To umožňuje například načíst tajné klíče na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2218a-420">Nepoužívejte předpony na tajných kódech trezoru klíčů k umístění tajných kódů pro více aplikací do stejného trezoru klíčů nebo k umístění environmentálních tajemství (například *ukvačování vývoje* versus *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="2218a-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="2218a-421">Doporučujeme, aby různé aplikace a vývojová a produkční prostředí používaly samostatné trezory klíčů k oddělení prostředí aplikací pro nejvyšší úroveň zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2218a-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="2218a-422">V následujícím příkladu je v trezoru klíčů vytvořen tajný klíč (a použití `5000-AppSecret` nástroje Správce tajných klíčů pro vývojové prostředí) (období nejsou povolena v názvech tajných klíčů).</span><span class="sxs-lookup"><span data-stu-id="2218a-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="2218a-423">Tento tajný klíč představuje tajný klíč aplikace pro verzi 5.0.0.0 aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="2218a-424">Pro jinou verzi aplikace, 5.1.0.0, je tajný klíč přidán do trezoru `5100-AppSecret`klíčů (a pomocí nástroje Správce tajných klíčů) pro .</span><span class="sxs-lookup"><span data-stu-id="2218a-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="2218a-425">Každá verze aplikace načte svou verzi `AppSecret`tajné hodnoty do své konfigurace jako , stripping off verzi, jak se načte tajný klíč.</span><span class="sxs-lookup"><span data-stu-id="2218a-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="2218a-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se nazývá s <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>vlastní :</span><span class="sxs-lookup"><span data-stu-id="2218a-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="2218a-427">Implementace <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na předpony verzí tajných kódů k načtení správného tajného klíče do konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="2218a-428">`Load`načte tajný klíč, když jeho název začíná předponou.</span><span class="sxs-lookup"><span data-stu-id="2218a-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="2218a-429">Ostatní tajemství nejsou nabitá.</span><span class="sxs-lookup"><span data-stu-id="2218a-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="2218a-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="2218a-430">`GetKey`:</span></span>
  * <span data-ttu-id="2218a-431">Odebere předponu z tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="2218a-432">Nahradí dvě pomlčky v libovolném názvu `KeyDelimiter`s , což je oddělovač používaný v konfiguraci (obvykle dvojtečka).</span><span class="sxs-lookup"><span data-stu-id="2218a-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="2218a-433">Azure Key Vault neumožňuje dvojtečku v tajných názvech.</span><span class="sxs-lookup"><span data-stu-id="2218a-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="2218a-434">Metoda `Load` je volána algoritmus zprostředkovatele, který itepraví přes tajné kódy trezoru najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="2218a-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="2218a-435">Pokud je nalezena předpona verze `Load` `GetKey` s , algoritmus používá metodu k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="2218a-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="2218a-436">Odstraní předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název názvu konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="2218a-437">Při provádění tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="2218a-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="2218a-438">Verze aplikace zadaná v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="2218a-439">V následujícím příkladu je verze aplikace `5.0.0.0`nastavena na :</span><span class="sxs-lookup"><span data-stu-id="2218a-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="2218a-440">Zkontrolujte, `<UserSecretsId>` zda je v souboru projektu `{GUID}` aplikace k dispozici vlastnost, kde je identifikátor GUID dodaný uživatelem:</span><span class="sxs-lookup"><span data-stu-id="2218a-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="2218a-441">Uložte následující tajné kódy místně pomocí [nástroje Správce tajných barev](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="2218a-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="2218a-442">Tajné klíče se ukládají do úložiště klíčů Azure pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="2218a-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="2218a-443">Při spuštění aplikace jsou načteny tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="2218a-444">Tajný klíč `5000-AppSecret` řetězce pro je spárován s verzí aplikace zadanou v`5.0.0.0`souboru projektu aplikace ( ).</span><span class="sxs-lookup"><span data-stu-id="2218a-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="2218a-445">Verze `5000` (s pomlčkou) je odstraněna z názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="2218a-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="2218a-446">V celé aplikaci čtení `AppSecret` konfigurace s klíčem načte tajnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2218a-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="2218a-447">Pokud se verze aplikace změní v souboru projektu a `5.1.0.0` aplikace se spustí `5.1.0.0_secret_value_dev` znovu, vrácená `5.1.0.0_secret_value_prod` tajná hodnota je ve vývojovém prostředí a v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="2218a-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="2218a-448">Můžete také poskytnout <xref:Microsoft.Azure.KeyVault.KeyVaultClient> vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementaci .</span><span class="sxs-lookup"><span data-stu-id="2218a-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="2218a-449">Vlastní klient umožňuje sdílení jedné instance klienta v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="2218a-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2218a-450">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="2218a-450">Bind an array to a class</span></span>

<span data-ttu-id="2218a-451">Zprostředkovatel je schopen číst hodnoty konfigurace do pole pro vazbu na pole POCO.</span><span class="sxs-lookup"><span data-stu-id="2218a-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="2218a-452">Při čtení ze zdroje konfigurace, který`:`umožňuje klíčům obsahovat oddělovače dvojtečky ( ),`:0:`se `:1:` &hellip; `:{n}:`k rozlišení klíčů tvořících pole používá číselný segment klíče ( , , ).</span><span class="sxs-lookup"><span data-stu-id="2218a-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="2218a-453">Další informace naleznete v [tématu Konfigurace: Vazba pole na třídu](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2218a-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="2218a-454">Klíče Azure Key Vault nelze použít dvojtečku jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="2218a-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="2218a-455">Přístup popsaný v tomto tématu používá`--`dvojité pomlčky ( ) jako oddělovač pro hierarchické hodnoty (oddíly).</span><span class="sxs-lookup"><span data-stu-id="2218a-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="2218a-456">Klíče pole jsou uloženy v trezoru klíčů Azure`--0--`s `--1--` &hellip; `--{n}--`dvojitými pomlčkami a číselnými segmenty klíčů ( , , ).</span><span class="sxs-lookup"><span data-stu-id="2218a-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="2218a-457">Prohlédněte si následující konfiguraci zprostředkovatele protokolování [Serilog](https://serilog.net/) poskytovou souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="2218a-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="2218a-458">V `WriteTo` poli jsou definovány dva literály objektu, které odrážejí dvě *jímky*Serilog , které popisují cíle pro protokolování výstupu:</span><span class="sxs-lookup"><span data-stu-id="2218a-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="2218a-459">Konfigurace zobrazená v předchozím souboru JSON je uložena`--`v azure key vaultu pomocí dvojité pomlčky ( ) zápisu a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="2218a-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="2218a-460">Klíč</span><span class="sxs-lookup"><span data-stu-id="2218a-460">Key</span></span> | <span data-ttu-id="2218a-461">Hodnota</span><span class="sxs-lookup"><span data-stu-id="2218a-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="2218a-462">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="2218a-462">Reload secrets</span></span>

<span data-ttu-id="2218a-463">Tajné klíče jsou `IConfigurationRoot.Reload()` ukládány do mezipaměti, dokud není volána.</span><span class="sxs-lookup"><span data-stu-id="2218a-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="2218a-464">Vypršela platnost, zakázáno a aktualizované tajné klíče v trezoru klíčů nejsou respektovány aplikací, dokud `Reload` není proveden.</span><span class="sxs-lookup"><span data-stu-id="2218a-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="2218a-465">Zakázané a prošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="2218a-465">Disabled and expired secrets</span></span>

<span data-ttu-id="2218a-466">Zakázané a vypršela <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>platnost tajných kódů hodit .</span><span class="sxs-lookup"><span data-stu-id="2218a-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="2218a-467">Chcete-li zabránit vyvolání aplikace, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázaný tajný klíč nebo tajný klíč, jehož platnost vypršela.</span><span class="sxs-lookup"><span data-stu-id="2218a-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2218a-468">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2218a-468">Troubleshoot</span></span>

<span data-ttu-id="2218a-469">Když se aplikaci nepodaří načíst konfiguraci pomocí zprostředkovatele, zapíše se chybová zpráva do [infrastruktury ASP.NET core logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2218a-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2218a-470">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="2218a-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="2218a-471">Aplikace nebo certifikát není správně nakonfigurovaný ve službě Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2218a-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="2218a-472">Trezor klíčů neexistuje v azure key vault.</span><span class="sxs-lookup"><span data-stu-id="2218a-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="2218a-473">Aplikace nemá oprávnění k přístupu do trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="2218a-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="2218a-474">Zásady přístupu `Get` neobsahuje `List` a oprávnění.</span><span class="sxs-lookup"><span data-stu-id="2218a-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="2218a-475">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela jejich platnost.</span><span class="sxs-lookup"><span data-stu-id="2218a-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="2218a-476">Aplikace má nesprávný název`KeyVaultName`trezoru klíčů ( ),`AzureADApplicationId`ID aplikace Azure AD ( ) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="2218a-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="2218a-477">Konfigurační klíč (název) je v aplikaci nesprávný pro hodnotu, kterou se pokoušíte načíst.</span><span class="sxs-lookup"><span data-stu-id="2218a-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="2218a-478">Při přidávání zásad přístupu pro aplikaci do trezoru klíčů byla zásada vytvořena, ale tlačítko **Uložit** nebylo v umělou iniciálním nastavení **zásad přístupu** vybráno.</span><span class="sxs-lookup"><span data-stu-id="2218a-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2218a-479">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2218a-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="2218a-480">Microsoft Azure: Trezor klíčů</span><span class="sxs-lookup"><span data-stu-id="2218a-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="2218a-481">Microsoft Azure: Dokumentace trezoru klíčů</span><span class="sxs-lookup"><span data-stu-id="2218a-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="2218a-482">Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2218a-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="2218a-483">Třída KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="2218a-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="2218a-484">Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="2218a-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="2218a-485">Kurz: Jak používat Azure Key Vault s Azure Windows Virtual Machine v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="2218a-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

