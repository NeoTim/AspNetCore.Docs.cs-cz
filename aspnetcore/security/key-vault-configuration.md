---
<span data-ttu-id="8e01c-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-102">'Blazor'</span></span>
- <span data-ttu-id="8e01c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-103">'Identity'</span></span>
- <span data-ttu-id="8e01c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-105">'Razor'</span></span>
- <span data-ttu-id="8e01c-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="8e01c-107">Poskytovatel konfigurace Azure Key Vault v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e01c-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="8e01c-108">Autor [: Andrew Stanton – zdravotní sestry](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8e01c-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e01c-109">Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8e01c-110">Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="8e01c-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8e01c-111">Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:</span><span class="sxs-lookup"><span data-stu-id="8e01c-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8e01c-112">Řízení přístupu k citlivým datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8e01c-113">Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="8e01c-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8e01c-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e01c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8e01c-115">Balíčky</span><span class="sxs-lookup"><span data-stu-id="8e01c-115">Packages</span></span>

<span data-ttu-id="8e01c-116">Přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8e01c-117">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="8e01c-117">Sample app</span></span>

<span data-ttu-id="8e01c-118">Ukázková aplikace se spustí v jednom ze dvou režimů určených `#define` příkazem v horní části souboru *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8e01c-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8e01c-119">`Certificate`: Demonstruje použití Azure Key Vault ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8e01c-120">Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e01c-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8e01c-121">`Managed`: Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření, že aplikace Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8e01c-122">Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="8e01c-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8e01c-123">`Managed`Verze ukázky musí být nasazená do Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8e01c-124">Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8e01c-125">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru ( `#define` ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8e01c-126">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="8e01c-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="8e01c-127">Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8e01c-128">Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8e01c-129">Nástroj Správce tajných klíčů vyžaduje `<UserSecretsId>` vlastnost v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8e01c-130">Nastavte hodnotu vlastnosti ( `{GUID}` ) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8e01c-131">Tajné kódy jsou vytvořeny jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8e01c-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8e01c-132">Hierarchické hodnoty (konfigurační oddíly) používají `:` jako oddělovač v [ASP.NET Core názvů konfiguračních](xref:fundamentals/configuration/index) klíčů středník (dvojtečku).</span><span class="sxs-lookup"><span data-stu-id="8e01c-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8e01c-133">Správce tajného kódu se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="8e01c-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8e01c-134">Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8e01c-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8e01c-135">Pokud jsou tyto tajné klíče uložené v Azure Key Vault v [tajném úložišti v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu, `_dev` přípona se změní na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8e01c-136">Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8e01c-137">Tajné úložiště v produkčním prostředí pomocí Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8e01c-138">Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="8e01c-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8e01c-139">Další podrobnosti najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8e01c-140">Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8e01c-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8e01c-141">Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8e01c-142">V textovém poli použijte hledaný řetězec "Azure CLI".</span><span class="sxs-lookup"><span data-stu-id="8e01c-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8e01c-143">Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8e01c-144">V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8e01c-145">Další informace najdete v tématu [Azure CLI](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8e01c-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8e01c-146">Pokud jste to ještě neověřili, přihlaste se pomocí `az login` příkazu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8e01c-147">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8e01c-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8e01c-148">Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8e01c-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8e01c-149">Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8e01c-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8e01c-150">Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="8e01c-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8e01c-151">Hierarchické hodnoty (konfigurační oddíly) používají `--` jako oddělovač (dvě pomlčky).</span><span class="sxs-lookup"><span data-stu-id="8e01c-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8e01c-152">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8e01c-153">Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.</span><span class="sxs-lookup"><span data-stu-id="8e01c-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8e01c-154">Následující tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="8e01c-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8e01c-155">Hodnoty zahrnují `_prod` příponu pro jejich rozlišení od `_dev` hodnot přípony načtených ve vývojovém prostředí od uživatelských tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8e01c-156">Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="8e01c-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8e01c-157">Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure</span><span class="sxs-lookup"><span data-stu-id="8e01c-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8e01c-158">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8e01c-159">Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8e01c-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8e01c-160">I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8e01c-161">Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8e01c-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8e01c-162">Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8e01c-163">Vytvořte certifikát PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8e01c-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8e01c-164">Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8e01c-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8e01c-165">Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="8e01c-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8e01c-166">Označení klíče jako exportovatelných je volitelné.</span><span class="sxs-lookup"><span data-stu-id="8e01c-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8e01c-167">Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8e01c-168">Exportujte certifikát PKCS # 12 (*. pfx*) jako certifikát kódovaný v kódování DER (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8e01c-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8e01c-169">Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).</span><span class="sxs-lookup"><span data-stu-id="8e01c-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8e01c-170">Nahrajte certifikát s kódováním DER (*. cer*) do Azure AD:</span><span class="sxs-lookup"><span data-stu-id="8e01c-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8e01c-171">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8e01c-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8e01c-172">Přejděte na **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8e01c-173">Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="8e01c-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8e01c-174">Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .</span><span class="sxs-lookup"><span data-stu-id="8e01c-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8e01c-175">V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8e01c-176">V Azure Portal přejděte na **trezory klíčů** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8e01c-177">[V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.</span><span class="sxs-lookup"><span data-stu-id="8e01c-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8e01c-178">Vyberte **Zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="8e01c-179">Vyberte **Přidat zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8e01c-180">Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8e01c-181">Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8e01c-182">Vyberte tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="8e01c-183">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-183">Select **OK**.</span></span>
1. <span data-ttu-id="8e01c-184">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-184">Select **Save**.</span></span>
1. <span data-ttu-id="8e01c-185">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e01c-185">Deploy the app.</span></span>

<span data-ttu-id="8e01c-186">`Certificate`Ukázková aplikace získá své konfigurační hodnoty ze `IConfigurationRoot` stejného názvu jako název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="8e01c-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8e01c-187">Nehierarchické hodnoty: hodnota pro `SecretName` je získána s `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8e01c-188">Hierarchické hodnoty (oddíly): použijte `:` notaci (dvojtečku) nebo `GetSection` metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8e01c-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8e01c-189">K získání hodnoty konfigurace použijte některý z těchto přístupů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8e01c-190">Certifikát X. 509 spravuje operační systém.</span><span class="sxs-lookup"><span data-stu-id="8e01c-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8e01c-191">Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> hodnoty zadané v souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8e01c-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8e01c-192">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="8e01c-192">Example values:</span></span>

* <span data-ttu-id="8e01c-193">Název trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8e01c-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8e01c-194">ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8e01c-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8e01c-195">Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8e01c-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8e01c-196">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8e01c-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8e01c-197">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8e01c-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8e01c-198">Ve vývojovém prostředí se tajné hodnoty načítají s `_dev` příponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8e01c-199">V produkčním prostředí se hodnoty načítají s `_prod` příponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8e01c-200">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="8e01c-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8e01c-201">**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo nebo tajný klíč klienta) uložené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e01c-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8e01c-202">Ukázková aplikace používá spravované identity pro prostředky Azure, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8e01c-203">Do souboru *appSettings. JSON* aplikace zadejte název trezoru.</span><span class="sxs-lookup"><span data-stu-id="8e01c-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8e01c-204">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená `Managed` verze, takže můžete tyto položky konfigurace ignorovat.</span><span class="sxs-lookup"><span data-stu-id="8e01c-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8e01c-205">Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8e01c-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8e01c-206">Nasaďte ukázkovou aplikaci do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="8e01c-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8e01c-207">Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8e01c-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8e01c-208">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8e01c-209">ID objektu se zobrazí v Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="8e01c-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8e01c-210">Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8e01c-211">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8e01c-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8e01c-212">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="8e01c-212">The sample app:</span></span>

* <span data-ttu-id="8e01c-213">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="8e01c-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8e01c-214">Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8e01c-215">Vytvoří <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se nový s `AzureServiceTokenProvider` voláním tokenu instance.</span><span class="sxs-lookup"><span data-stu-id="8e01c-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8e01c-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Instance se používá s výchozí implementací <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která načte všechny tajné hodnoty a nahradí dvojité spojovníky ( `--` ) dvojtečkami ( `:` ) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8e01c-217">Ukázková hodnota názvu trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8e01c-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8e01c-218">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8e01c-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8e01c-219">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8e01c-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8e01c-220">Ve vývojovém prostředí mají tajné hodnoty příponu, `_dev` protože jsou poskytovány uživatelskými tajemstvími.</span><span class="sxs-lookup"><span data-stu-id="8e01c-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8e01c-221">V produkčním prostředí se hodnoty načítají s `_prod` příponou, protože jsou k dispozici v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8e01c-222">Pokud se zobrazí `Access denied` Chyba, zkontrolujte, že je aplikace zaregistrovaná ve službě Azure AD a že poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8e01c-223">Potvrďte, že jste službu restartovali v Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8e01c-224">Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete v tématu [vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8e01c-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="8e01c-225">Možnosti konfigurace</span><span class="sxs-lookup"><span data-stu-id="8e01c-225">Configuration options</span></span>

<span data-ttu-id="8e01c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>může přijmout <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="8e01c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="8e01c-227">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="8e01c-227">Property</span></span>         | <span data-ttu-id="8e01c-228">Description</span><span class="sxs-lookup"><span data-stu-id="8e01c-228">Description</span></span> |
| ---
<span data-ttu-id="8e01c-229">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-230">'Blazor'</span></span>
- <span data-ttu-id="8e01c-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-231">'Identity'</span></span>
- <span data-ttu-id="8e01c-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-233">'Razor'</span></span>
- <span data-ttu-id="8e01c-234">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-235">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-236">'Blazor'</span></span>
- <span data-ttu-id="8e01c-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-237">'Identity'</span></span>
- <span data-ttu-id="8e01c-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-239">'Razor'</span></span>
- <span data-ttu-id="8e01c-240">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-241">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-242">'Blazor'</span></span>
- <span data-ttu-id="8e01c-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-243">'Identity'</span></span>
- <span data-ttu-id="8e01c-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-245">'Razor'</span></span>
- <span data-ttu-id="8e01c-246">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-247">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-248">'Blazor'</span></span>
- <span data-ttu-id="8e01c-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-249">'Identity'</span></span>
- <span data-ttu-id="8e01c-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-251">'Razor'</span></span>
- <span data-ttu-id="8e01c-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-253">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-254">'Blazor'</span></span>
- <span data-ttu-id="8e01c-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-255">'Identity'</span></span>
- <span data-ttu-id="8e01c-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-257">'Razor'</span></span>
- <span data-ttu-id="8e01c-258">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-259">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-260">'Blazor'</span></span>
- <span data-ttu-id="8e01c-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-261">'Identity'</span></span>
- <span data-ttu-id="8e01c-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-263">'Razor'</span></span>
- <span data-ttu-id="8e01c-264">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-264">'SignalR' uid:</span></span> 

<span data-ttu-id="8e01c-265">-------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-266">'Blazor'</span></span>
- <span data-ttu-id="8e01c-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-267">'Identity'</span></span>
- <span data-ttu-id="8e01c-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-269">'Razor'</span></span>
- <span data-ttu-id="8e01c-270">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-271">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-272">'Blazor'</span></span>
- <span data-ttu-id="8e01c-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-273">'Identity'</span></span>
- <span data-ttu-id="8e01c-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-275">'Razor'</span></span>
- <span data-ttu-id="8e01c-276">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8e01c-277">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="8e01c-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8e01c-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-278">'Blazor'</span></span>
- <span data-ttu-id="8e01c-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e01c-279">'Identity'</span></span>
- <span data-ttu-id="8e01c-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e01c-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="8e01c-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e01c-281">'Razor'</span></span>
- <span data-ttu-id="8e01c-282">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-282">'SignalR' uid:</span></span> 

<span data-ttu-id="8e01c-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> slouží k načtení hodnot.</span><span class="sxs-lookup"><span data-stu-id="8e01c-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="8e01c-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance, která slouží k řízení načítání tajného klíče.</span><span class="sxs-lookup"><span data-stu-id="8e01c-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="8e01c-285">| | `ReloadInterval` | `Timespan` pro čekání na změny mezi pokusy o cyklické dotazování trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="8e01c-286">Výchozí hodnota je `null` (konfigurace není znovu načtena).</span><span class="sxs-lookup"><span data-stu-id="8e01c-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="8e01c-287">| | `Vault`          | Identifikátor URI trezoru klíčů</span><span class="sxs-lookup"><span data-stu-id="8e01c-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8e01c-288">Použít předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="8e01c-288">Use a key name prefix</span></span>

<span data-ttu-id="8e01c-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která umožňuje řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8e01c-290">Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8e01c-291">To vám umožní například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8e01c-292">Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="8e01c-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8e01c-293">Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="8e01c-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8e01c-294">V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (období nejsou v tajných názvech trezoru klíčů povolená).</span><span class="sxs-lookup"><span data-stu-id="8e01c-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8e01c-295">Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8e01c-296">V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8e01c-297">Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret` a při načtení tajného kódu vymění verzi.</span><span class="sxs-lookup"><span data-stu-id="8e01c-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8e01c-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>je volána s vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="8e01c-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8e01c-299"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementace reaguje na prefixy verze tajných kódů, aby se do konfigurace načetl správný tajný kód:</span><span class="sxs-lookup"><span data-stu-id="8e01c-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8e01c-300">`Load`Načte tajný klíč, pokud jeho název začíná předponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8e01c-301">Další tajné kódy nejsou načteny.</span><span class="sxs-lookup"><span data-stu-id="8e01c-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8e01c-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8e01c-302">`GetKey`:</span></span>
  * <span data-ttu-id="8e01c-303">Odebere předponu z názvu tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8e01c-304">Nahradí dvě pomlčky v libovolném názvu pomocí `KeyDelimiter` , což je oddělovač použitý v konfiguraci (obvykle dvojtečka).</span><span class="sxs-lookup"><span data-stu-id="8e01c-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8e01c-305">Azure Key Vault v tajných názvech nepovoluje dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="8e01c-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8e01c-306">`Load`Metoda je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="8e01c-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8e01c-307">Pokud je nalezena předpona verze s `Load` , algoritmus používá `GetKey` metodu k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8e01c-308">Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8e01c-309">Při implementaci tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="8e01c-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="8e01c-310">Verze aplikace zadaná v souboru projektu aplikace</span><span class="sxs-lookup"><span data-stu-id="8e01c-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8e01c-311">V následujícím příkladu je verze aplikace nastavená na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8e01c-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8e01c-312">Zkontrolujte, zda je `<UserSecretsId>` v souboru projektu aplikace přítomna vlastnost, kde `{GUID}` je uživatelem zadaný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8e01c-313">Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8e01c-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8e01c-314">Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="8e01c-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8e01c-315">Po spuštění aplikace se načtou tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8e01c-316">Řetězcová tajná `5000-AppSecret` verze pro je shodná s verzí aplikace zadanou v souboru projektu aplikace ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8e01c-317">Verze `5000` (s pomlčkou) je z názvu klíče odstraněna.</span><span class="sxs-lookup"><span data-stu-id="8e01c-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8e01c-318">V celé aplikaci čtení konfigurace pomocí klíče `AppSecret` načte tajnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8e01c-319">Pokud se v souboru projektu změní verze aplikace na `5.1.0.0` a aplikace se znovu spustí, vrátí tajná hodnota `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8e01c-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8e01c-320">Můžete také zadat vlastní <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementaci do <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8e01c-321">Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.</span><span class="sxs-lookup"><span data-stu-id="8e01c-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8e01c-322">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="8e01c-322">Bind an array to a class</span></span>

<span data-ttu-id="8e01c-323">Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="8e01c-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8e01c-324">Při čtení ze zdroje konfigurace, který umožňuje klíčům, aby obsahoval `:` oddělovače dvojtečky (), je k odlišení klíčů, které tvoří pole ( `:0:` ,,), použit numerický segment klíče `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8e01c-325">Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8e01c-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8e01c-326">Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="8e01c-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8e01c-327">Přístup popsaný v tomto tématu používá `--` pro hierarchické hodnoty (oddíly) dvojité pomlčky () jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="8e01c-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8e01c-328">Klíče pole jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8e01c-329">Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="8e01c-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8e01c-330">V poli jsou definovány dva literály objektů, `WriteTo` které odrážejí dvě Serilog *jímky*, které popisují umístění pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="8e01c-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8e01c-331">Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s použitím dvojité čárky ( `--` ) Notation a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8e01c-332">Klíč</span><span class="sxs-lookup"><span data-stu-id="8e01c-332">Key</span></span> | <span data-ttu-id="8e01c-333">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8e01c-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8e01c-334">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="8e01c-334">Reload secrets</span></span>

<span data-ttu-id="8e01c-335">Tajné kódy jsou ukládány do mezipaměti `IConfigurationRoot.Reload()` , dokud není volána.</span><span class="sxs-lookup"><span data-stu-id="8e01c-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8e01c-336">V případě vypršení platnosti, zakázání a aktualizovaných tajných klíčů v trezoru klíčů se aplikace nedodržuje, dokud `Reload` se neprovede.</span><span class="sxs-lookup"><span data-stu-id="8e01c-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8e01c-337">Zakázané a neprošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="8e01c-337">Disabled and expired secrets</span></span>

<span data-ttu-id="8e01c-338">Zakázané a neprošlé tajné klíče vyvolají <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8e01c-339">Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="8e01c-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8e01c-340">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="8e01c-340">Troubleshoot</span></span>

<span data-ttu-id="8e01c-341">Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="8e01c-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8e01c-342">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8e01c-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8e01c-343">Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8e01c-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8e01c-344">Trezor klíčů neexistuje v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8e01c-345">Aplikace nemá autorizaci pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8e01c-346">Zásady přístupu nezahrnují `Get` oprávnění a `List` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8e01c-347">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.</span><span class="sxs-lookup"><span data-stu-id="8e01c-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8e01c-348">Aplikace má nesprávný název trezoru klíčů ( `KeyVaultName` ), ID aplikace Azure AD () `AzureADApplicationId` nebo kryptografický otisk certifikátu Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8e01c-349">Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.</span><span class="sxs-lookup"><span data-stu-id="8e01c-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8e01c-350">Když přidáte zásady přístupu pro aplikaci do trezoru klíčů, zásada se vytvořila, ale tlačítko **Uložit** nebylo vybrané v uživatelském rozhraní **zásad přístupu** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e01c-351">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8e01c-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8e01c-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8e01c-353">Microsoft Azure: dokumentace Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8e01c-354">Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8e01c-355">KeyVaultClient – třída</span><span class="sxs-lookup"><span data-stu-id="8e01c-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8e01c-356">Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="8e01c-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8e01c-357">Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET</span><span class="sxs-lookup"><span data-stu-id="8e01c-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e01c-358">Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8e01c-359">Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami.</span><span class="sxs-lookup"><span data-stu-id="8e01c-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8e01c-360">Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:</span><span class="sxs-lookup"><span data-stu-id="8e01c-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8e01c-361">Řízení přístupu k citlivým datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8e01c-362">Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.</span><span class="sxs-lookup"><span data-stu-id="8e01c-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8e01c-363">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e01c-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8e01c-364">Balíčky</span><span class="sxs-lookup"><span data-stu-id="8e01c-364">Packages</span></span>

<span data-ttu-id="8e01c-365">Přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8e01c-366">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="8e01c-366">Sample app</span></span>

<span data-ttu-id="8e01c-367">Ukázková aplikace se spustí v jednom ze dvou režimů určených `#define` příkazem v horní části souboru *program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8e01c-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8e01c-368">`Certificate`: Demonstruje použití Azure Key Vault ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8e01c-369">Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e01c-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8e01c-370">`Managed`: Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření, že aplikace Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8e01c-371">Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta).</span><span class="sxs-lookup"><span data-stu-id="8e01c-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8e01c-372">`Managed`Verze ukázky musí být nasazená do Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8e01c-373">Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8e01c-374">Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru ( `#define` ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8e01c-375">Tajné úložiště ve vývojovém prostředí</span><span class="sxs-lookup"><span data-stu-id="8e01c-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="8e01c-376">Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8e01c-377">Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8e01c-378">Nástroj Správce tajných klíčů vyžaduje `<UserSecretsId>` vlastnost v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8e01c-379">Nastavte hodnotu vlastnosti ( `{GUID}` ) na libovolný jedinečný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8e01c-380">Tajné kódy jsou vytvořeny jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8e01c-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8e01c-381">Hierarchické hodnoty (konfigurační oddíly) používají `:` jako oddělovač v [ASP.NET Core názvů konfiguračních](xref:fundamentals/configuration/index) klíčů středník (dvojtečku).</span><span class="sxs-lookup"><span data-stu-id="8e01c-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8e01c-382">Správce tajného kódu se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:</span><span class="sxs-lookup"><span data-stu-id="8e01c-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8e01c-383">Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="8e01c-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8e01c-384">Pokud jsou tyto tajné klíče uložené v Azure Key Vault v [tajném úložišti v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu, `_dev` přípona se změní na `_prod` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8e01c-385">Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8e01c-386">Tajné úložiště v produkčním prostředí pomocí Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8e01c-387">Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="8e01c-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8e01c-388">Další podrobnosti najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8e01c-389">Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8e01c-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8e01c-390">Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8e01c-391">V textovém poli použijte hledaný řetězec "Azure CLI".</span><span class="sxs-lookup"><span data-stu-id="8e01c-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8e01c-392">Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8e01c-393">V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8e01c-394">Další informace najdete v tématu [Azure CLI](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8e01c-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8e01c-395">Pokud jste to ještě neověřili, přihlaste se pomocí `az login` příkazu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8e01c-396">Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8e01c-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8e01c-397">Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` oblast Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8e01c-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8e01c-398">Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8e01c-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8e01c-399">Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky.</span><span class="sxs-lookup"><span data-stu-id="8e01c-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8e01c-400">Hierarchické hodnoty (konfigurační oddíly) používají `--` jako oddělovač (dvě pomlčky).</span><span class="sxs-lookup"><span data-stu-id="8e01c-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8e01c-401">Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8e01c-402">Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.</span><span class="sxs-lookup"><span data-stu-id="8e01c-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8e01c-403">Následující tajné klíče jsou pro použití s ukázkovou aplikací.</span><span class="sxs-lookup"><span data-stu-id="8e01c-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8e01c-404">Hodnoty zahrnují `_prod` příponu pro jejich rozlišení od `_dev` hodnot přípony načtených ve vývojovém prostředí od uživatelských tajných kódů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8e01c-405">Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:</span><span class="sxs-lookup"><span data-stu-id="8e01c-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8e01c-406">Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure</span><span class="sxs-lookup"><span data-stu-id="8e01c-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8e01c-407">Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8e01c-408">Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8e01c-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8e01c-409">I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8e01c-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8e01c-410">Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8e01c-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8e01c-411">Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8e01c-412">Vytvořte certifikát PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8e01c-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8e01c-413">Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8e01c-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8e01c-414">Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="8e01c-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8e01c-415">Označení klíče jako exportovatelných je volitelné.</span><span class="sxs-lookup"><span data-stu-id="8e01c-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8e01c-416">Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8e01c-417">Exportujte certifikát PKCS # 12 (*. pfx*) jako certifikát kódovaný v kódování DER (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="8e01c-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8e01c-418">Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).</span><span class="sxs-lookup"><span data-stu-id="8e01c-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8e01c-419">Nahrajte certifikát s kódováním DER (*. cer*) do Azure AD:</span><span class="sxs-lookup"><span data-stu-id="8e01c-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8e01c-420">Vyberte aplikaci ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8e01c-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8e01c-421">Přejděte na **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8e01c-422">Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč.</span><span class="sxs-lookup"><span data-stu-id="8e01c-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8e01c-423">Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .</span><span class="sxs-lookup"><span data-stu-id="8e01c-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8e01c-424">V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8e01c-425">V Azure Portal přejděte na **trezory klíčů** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8e01c-426">[V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.</span><span class="sxs-lookup"><span data-stu-id="8e01c-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8e01c-427">Vyberte **Zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="8e01c-428">Vyberte **Přidat zásady přístupu**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="8e01c-429">Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8e01c-430">Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8e01c-431">Vyberte tlačítko **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="8e01c-432">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-432">Select **OK**.</span></span>
1. <span data-ttu-id="8e01c-433">Vyberte **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="8e01c-433">Select **Save**.</span></span>
1. <span data-ttu-id="8e01c-434">Nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e01c-434">Deploy the app.</span></span>

<span data-ttu-id="8e01c-435">`Certificate`Ukázková aplikace získá své konfigurační hodnoty ze `IConfigurationRoot` stejného názvu jako název tajného kódu:</span><span class="sxs-lookup"><span data-stu-id="8e01c-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8e01c-436">Nehierarchické hodnoty: hodnota pro `SecretName` je získána s `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8e01c-437">Hierarchické hodnoty (oddíly): použijte `:` notaci (dvojtečku) nebo `GetSection` metodu rozšíření.</span><span class="sxs-lookup"><span data-stu-id="8e01c-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8e01c-438">K získání hodnoty konfigurace použijte některý z těchto přístupů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8e01c-439">Certifikát X. 509 spravuje operační systém.</span><span class="sxs-lookup"><span data-stu-id="8e01c-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8e01c-440">Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> hodnoty zadané v souboru *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8e01c-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8e01c-441">Příklady hodnot:</span><span class="sxs-lookup"><span data-stu-id="8e01c-441">Example values:</span></span>

* <span data-ttu-id="8e01c-442">Název trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8e01c-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8e01c-443">ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8e01c-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8e01c-444">Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8e01c-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8e01c-445">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8e01c-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="8e01c-446">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8e01c-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8e01c-447">Ve vývojovém prostředí se tajné hodnoty načítají s `_dev` příponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8e01c-448">V produkčním prostředí se hodnoty načítají s `_prod` příponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8e01c-449">Použití spravovaných identit pro prostředky Azure</span><span class="sxs-lookup"><span data-stu-id="8e01c-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8e01c-450">**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo nebo tajný klíč klienta) uložené v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8e01c-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8e01c-451">Ukázková aplikace používá spravované identity pro prostředky Azure, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Managed` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8e01c-452">Do souboru *appSettings. JSON* aplikace zadejte název trezoru.</span><span class="sxs-lookup"><span data-stu-id="8e01c-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8e01c-453">Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená `Managed` verze, takže můžete tyto položky konfigurace ignorovat.</span><span class="sxs-lookup"><span data-stu-id="8e01c-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8e01c-454">Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8e01c-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8e01c-455">Nasaďte ukázkovou aplikaci do Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="8e01c-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8e01c-456">Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8e01c-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8e01c-457">Získejte ID objektu z nasazení pro použití v následujícím příkazu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8e01c-458">ID objektu se zobrazí v Azure Portal na **Identity** panelu App Service.</span><span class="sxs-lookup"><span data-stu-id="8e01c-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8e01c-459">Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci `list` a `get` oprávnění pro přístup k trezoru klíčů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8e01c-460">**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8e01c-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8e01c-461">Ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="8e01c-461">The sample app:</span></span>

* <span data-ttu-id="8e01c-462">Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="8e01c-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8e01c-463">Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8e01c-464">Vytvoří <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se nový s `AzureServiceTokenProvider` voláním tokenu instance.</span><span class="sxs-lookup"><span data-stu-id="8e01c-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8e01c-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Instance se používá s výchozí implementací <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která načte všechny tajné hodnoty a nahradí dvojité spojovníky ( `--` ) dvojtečkami ( `:` ) v názvech klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8e01c-466">Ukázková hodnota názvu trezoru klíčů:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="8e01c-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8e01c-467">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8e01c-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8e01c-468">Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8e01c-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8e01c-469">Ve vývojovém prostředí mají tajné hodnoty příponu, `_dev` protože jsou poskytovány uživatelskými tajemstvími.</span><span class="sxs-lookup"><span data-stu-id="8e01c-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8e01c-470">V produkčním prostředí se hodnoty načítají s `_prod` příponou, protože jsou k dispozici v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8e01c-471">Pokud se zobrazí `Access denied` Chyba, zkontrolujte, že je aplikace zaregistrovaná ve službě Azure AD a že poskytuje přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8e01c-472">Potvrďte, že jste službu restartovali v Azure.</span><span class="sxs-lookup"><span data-stu-id="8e01c-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8e01c-473">Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete v tématu [vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8e01c-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8e01c-474">Použít předponu názvu klíče</span><span class="sxs-lookup"><span data-stu-id="8e01c-474">Use a key name prefix</span></span>

<span data-ttu-id="8e01c-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která umožňuje řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8e01c-476">Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8e01c-477">To vám umožní například načíst tajné kódy na základě verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8e01c-478">Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru.</span><span class="sxs-lookup"><span data-stu-id="8e01c-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8e01c-479">Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="8e01c-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8e01c-480">V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (období nejsou v tajných názvech trezoru klíčů povolená).</span><span class="sxs-lookup"><span data-stu-id="8e01c-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8e01c-481">Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8e01c-482">V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8e01c-483">Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret` a při načtení tajného kódu vymění verzi.</span><span class="sxs-lookup"><span data-stu-id="8e01c-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8e01c-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>je volána s vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="8e01c-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="8e01c-485"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementace reaguje na prefixy verze tajných kódů, aby se do konfigurace načetl správný tajný kód:</span><span class="sxs-lookup"><span data-stu-id="8e01c-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="8e01c-486">`Load`Načte tajný klíč, pokud jeho název začíná předponou.</span><span class="sxs-lookup"><span data-stu-id="8e01c-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="8e01c-487">Další tajné kódy nejsou načteny.</span><span class="sxs-lookup"><span data-stu-id="8e01c-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="8e01c-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="8e01c-488">`GetKey`:</span></span>
  * <span data-ttu-id="8e01c-489">Odebere předponu z názvu tajného kódu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="8e01c-490">Nahradí dvě pomlčky v libovolném názvu pomocí `KeyDelimiter` , což je oddělovač použitý v konfiguraci (obvykle dvojtečka).</span><span class="sxs-lookup"><span data-stu-id="8e01c-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="8e01c-491">Azure Key Vault v tajných názvech nepovoluje dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="8e01c-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="8e01c-492">`Load`Metoda je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze.</span><span class="sxs-lookup"><span data-stu-id="8e01c-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8e01c-493">Pokud je nalezena předpona verze s `Load` , algoritmus používá `GetKey` metodu k vrácení názvu konfigurace tajného názvu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8e01c-494">Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="8e01c-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8e01c-495">Při implementaci tohoto přístupu:</span><span class="sxs-lookup"><span data-stu-id="8e01c-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="8e01c-496">Verze aplikace zadaná v souboru projektu aplikace</span><span class="sxs-lookup"><span data-stu-id="8e01c-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8e01c-497">V následujícím příkladu je verze aplikace nastavená na `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="8e01c-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8e01c-498">Zkontrolujte, zda je `<UserSecretsId>` v souboru projektu aplikace přítomna vlastnost, kde `{GUID}` je uživatelem zadaný identifikátor GUID:</span><span class="sxs-lookup"><span data-stu-id="8e01c-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8e01c-499">Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8e01c-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8e01c-500">Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="8e01c-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8e01c-501">Po spuštění aplikace se načtou tajné klíče trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8e01c-502">Řetězcová tajná `5000-AppSecret` verze pro je shodná s verzí aplikace zadanou v souboru projektu aplikace ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8e01c-503">Verze `5000` (s pomlčkou) je z názvu klíče odstraněna.</span><span class="sxs-lookup"><span data-stu-id="8e01c-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8e01c-504">V celé aplikaci čtení konfigurace pomocí klíče `AppSecret` načte tajnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8e01c-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8e01c-505">Pokud se v souboru projektu změní verze aplikace na `5.1.0.0` a aplikace se znovu spustí, vrátí tajná hodnota `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8e01c-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8e01c-506">Můžete také zadat vlastní <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementaci do <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="8e01c-507">Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.</span><span class="sxs-lookup"><span data-stu-id="8e01c-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8e01c-508">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="8e01c-508">Bind an array to a class</span></span>

<span data-ttu-id="8e01c-509">Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.</span><span class="sxs-lookup"><span data-stu-id="8e01c-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8e01c-510">Při čtení ze zdroje konfigurace, který umožňuje klíčům, aby obsahoval `:` oddělovače dvojtečky (), je k odlišení klíčů, které tvoří pole ( `:0:` ,,), použit numerický segment klíče `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="8e01c-511">Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8e01c-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8e01c-512">Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="8e01c-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8e01c-513">Přístup popsaný v tomto tématu používá `--` pro hierarchické hodnoty (oddíly) dvojité pomlčky () jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="8e01c-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8e01c-514">Klíče pole jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8e01c-515">Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON.</span><span class="sxs-lookup"><span data-stu-id="8e01c-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8e01c-516">V poli jsou definovány dva literály objektů, `WriteTo` které odrážejí dvě Serilog *jímky*, které popisují umístění pro výstup protokolování:</span><span class="sxs-lookup"><span data-stu-id="8e01c-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8e01c-517">Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s použitím dvojité čárky ( `--` ) Notation a číselných segmentů:</span><span class="sxs-lookup"><span data-stu-id="8e01c-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8e01c-518">Klíč</span><span class="sxs-lookup"><span data-stu-id="8e01c-518">Key</span></span> | <span data-ttu-id="8e01c-519">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8e01c-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8e01c-520">Znovu načíst tajné kódy</span><span class="sxs-lookup"><span data-stu-id="8e01c-520">Reload secrets</span></span>

<span data-ttu-id="8e01c-521">Tajné kódy jsou ukládány do mezipaměti `IConfigurationRoot.Reload()` , dokud není volána.</span><span class="sxs-lookup"><span data-stu-id="8e01c-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8e01c-522">V případě vypršení platnosti, zakázání a aktualizovaných tajných klíčů v trezoru klíčů se aplikace nedodržuje, dokud `Reload` se neprovede.</span><span class="sxs-lookup"><span data-stu-id="8e01c-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8e01c-523">Zakázané a neprošlé tajné klíče</span><span class="sxs-lookup"><span data-stu-id="8e01c-523">Disabled and expired secrets</span></span>

<span data-ttu-id="8e01c-524">Zakázané a neprošlé tajné klíče vyvolají <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="8e01c-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="8e01c-525">Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="8e01c-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8e01c-526">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="8e01c-526">Troubleshoot</span></span>

<span data-ttu-id="8e01c-527">Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="8e01c-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8e01c-528">Následující podmínky zabrání načtení konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8e01c-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8e01c-529">Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8e01c-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8e01c-530">Trezor klíčů neexistuje v Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8e01c-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8e01c-531">Aplikace nemá autorizaci pro přístup k trezoru klíčů.</span><span class="sxs-lookup"><span data-stu-id="8e01c-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8e01c-532">Zásady přístupu nezahrnují `Get` oprávnění a `List` .</span><span class="sxs-lookup"><span data-stu-id="8e01c-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8e01c-533">V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.</span><span class="sxs-lookup"><span data-stu-id="8e01c-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8e01c-534">Aplikace má nesprávný název trezoru klíčů ( `KeyVaultName` ), ID aplikace Azure AD () `AzureADApplicationId` nebo kryptografický otisk certifikátu Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="8e01c-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8e01c-535">Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.</span><span class="sxs-lookup"><span data-stu-id="8e01c-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="8e01c-536">Když přidáte zásady přístupu pro aplikaci do trezoru klíčů, zásada se vytvořila, ale tlačítko **Uložit** nebylo vybrané v uživatelském rozhraní **zásad přístupu** .</span><span class="sxs-lookup"><span data-stu-id="8e01c-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e01c-537">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8e01c-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8e01c-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8e01c-539">Microsoft Azure: dokumentace Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8e01c-540">Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e01c-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8e01c-541">KeyVaultClient – třída</span><span class="sxs-lookup"><span data-stu-id="8e01c-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8e01c-542">Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="8e01c-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8e01c-543">Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET</span><span class="sxs-lookup"><span data-stu-id="8e01c-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

