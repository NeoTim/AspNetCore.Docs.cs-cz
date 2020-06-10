---
title: Poskytovatel konfigurace Azure Key Vault v ASP.NET Core
author: rick-anderson
description: Naučte se používat zprostředkovatele konfigurace Azure Key Vault ke konfiguraci aplikace pomocí dvojic název-hodnota načtené za běhu.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/key-vault-configuration
ms.openlocfilehash: 4a5689af9ffea175838a869e92752de889cbb227
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106673"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Poskytovatel konfigurace Azure Key Vault v ASP.NET Core

Autor [: Andrew Stanton – zdravotní sestry](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů. Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami. Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:

* Řízení přístupu k citlivým datům konfigurace.
* Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Balíčky

Přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Ukázková aplikace

Ukázková aplikace se spustí v jednom ze dvou režimů určených `#define` příkazem v horní části souboru *program.cs* :

* `Certificate`: Demonstruje použití Azure Key Vault ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault. Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.
* `Managed`: Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření, že aplikace Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace. Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta). `Managed`Verze ukázky musí být nasazená do Azure. Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .

Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru ( `#define` ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Tajné úložiště ve vývojovém prostředí

Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů. Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.

Nástroj Správce tajných klíčů vyžaduje `<UserSecretsId>` vlastnost v souboru projektu aplikace. Nastavte hodnotu vlastnosti ( `{GUID}` ) na libovolný jedinečný identifikátor GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Tajné kódy jsou vytvořeny jako páry název-hodnota. Hierarchické hodnoty (konfigurační oddíly) používají `:` jako oddělovač v [ASP.NET Core názvů konfiguračních](xref:fundamentals/configuration/index) klíčů středník (dvojtečku).

Správce tajného kódu se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Pokud jsou tyto tajné klíče uložené v Azure Key Vault v [tajném úložišti v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu, `_dev` přípona se změní na `_prod` . Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajné úložiště v produkčním prostředí pomocí Azure Key Vault

Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací. Další podrobnosti najdete v tématu.

1. Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):

   * Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. V textovém poli použijte hledaný řetězec "Azure CLI".
   * Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .
   * V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .

   Další informace najdete v tématu [Azure CLI](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pokud jste to ještě neověřili, přihlaste se pomocí `az login` příkazu.

1. Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` oblast Azure (Datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` oblast Azure (Datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.

   Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky. Hierarchické hodnoty (konfigurační oddíly) používají `--` jako oddělovač (dvě pomlčky). Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů. Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.

   Následující tajné klíče jsou pro použití s ukázkovou aplikací. Hodnoty zahrnují `_prod` příponu pro jejich rozlišení od `_dev` hodnot přípony načtených ve vývojovém prostředí od uživatelských tajných kódů. Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure

Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**. Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) . Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.

Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Certificate` .

1. Vytvořte certifikát PKCS # 12 (*. pfx*). Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).
1. Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele. Označení klíče jako exportovatelných je volitelné. Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.
1. Exportujte certifikát PKCS # 12 (*. pfx*) jako certifikát kódovaný v kódování DER (*. cer*).
1. Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).
1. Nahrajte certifikát s kódováním DER (*. cer*) do Azure AD:
   1. Vyberte aplikaci ve službě Azure AD.
   1. Přejděte na **certifikáty & tajných**kódů.
   1. Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč. Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .
1. V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.
1. V Azure Portal přejděte na **trezory klíčů** .
1. [V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.
1. Vyberte **Zásady přístupu**.
1. Vyberte **Přidat zásady přístupu**.
1. Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .
1. Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu. Vyberte tlačítko **Vybrat** .
1. Vyberte **OK**.
1. Vyberte **Uložit**.
1. Nasaďte aplikaci.

`Certificate`Ukázková aplikace získá své konfigurační hodnoty ze `IConfigurationRoot` stejného názvu jako název tajného kódu:

* Nehierarchické hodnoty: hodnota pro `SecretName` je získána s `config["SecretName"]` .
* Hierarchické hodnoty (oddíly): použijte `:` notaci (dvojtečku) nebo `GetSection` metodu rozšíření. K získání hodnoty konfigurace použijte některý z těchto přístupů:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certifikát X. 509 spravuje operační systém. Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> hodnoty zadané v souboru *appSettings. JSON* :

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Příklady hodnot:

* Název trezoru klíčů:`contosovault`
* ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`
* Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appSettings. JSON*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty. Ve vývojovém prostředí se tajné hodnoty načítají s `_dev` příponou. V produkčním prostředí se hodnoty načítají s `_prod` příponou.

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravovaných identit pro prostředky Azure

**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo nebo tajný klíč klienta) uložené v aplikaci.

Ukázková aplikace používá spravované identity pro prostředky Azure, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Managed` .

Do souboru *appSettings. JSON* aplikace zadejte název trezoru. Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená `Managed` verze, takže můžete tyto položky konfigurace ignorovat. Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .

Nasaďte ukázkovou aplikaci do Azure App Service.

Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD. Získejte ID objektu z nasazení pro použití v následujícím příkazu. ID objektu se zobrazí v Azure Portal na **Identity** panelu App Service.

Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci `list` a `get` oprávnění pro přístup k trezoru klíčů:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.

Ukázková aplikace:

* Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce. Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.
* Vytvoří <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se nový s `AzureServiceTokenProvider` voláním tokenu instance.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Instance se používá s výchozí implementací <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která načte všechny tajné hodnoty a nahradí dvojité spojovníky ( `--` ) dvojtečkami ( `:` ) v názvech klíčů.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Ukázková hodnota názvu trezoru klíčů:`contosovault`
    
*appSettings. JSON*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty. Ve vývojovém prostředí mají tajné hodnoty příponu, `_dev` protože jsou poskytovány uživatelskými tajemstvími. V produkčním prostředí se hodnoty načítají s `_prod` příponou, protože jsou k dispozici v Azure Key Vault.

Pokud se zobrazí `Access denied` Chyba, zkontrolujte, že je aplikace zaregistrovaná ve službě Azure AD a že poskytuje přístup k trezoru klíčů. Potvrďte, že jste službu restartovali v Azure.

Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete v tématu [vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Možnosti konfigurace

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>může přijmout <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Vlastnost         | Popis |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>slouží k načtení hodnot. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instance, která slouží k řízení načítání tajného klíče. |
| `ReloadInterval` | `Timespan`pro čekání na změny mezi pokusy o cyklické dotazování trezoru klíčů. Výchozí hodnota je `null` (konfigurace není znovu načtena). |
| `Vault`          | Identifikátor URI trezoru klíčů |

## <a name="use-a-key-name-prefix"></a>Použít předponu názvu klíče

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která umožňuje řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů. Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace. To vám umožní například načíst tajné kódy na základě verze aplikace.

> [!WARNING]
> Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru. Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.

V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (období nejsou v tajných názvech trezoru klíčů povolená). Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace. V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret` . Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret` a při načtení tajného kódu vymění verzi.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>je volána s vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementace reaguje na prefixy verze tajných kódů, aby se do konfigurace načetl správný tajný kód:

* `Load`Načte tajný klíč, pokud jeho název začíná předponou. Další tajné kódy nejsou načteny.
* `GetKey`:
  * Odebere předponu z názvu tajného kódu.
  * Nahradí dvě pomlčky v libovolném názvu pomocí `KeyDelimiter` , což je oddělovač použitý v konfiguraci (obvykle dvojtečka). Azure Key Vault v tajných názvech nepovoluje dvojtečku.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Metoda je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze. Pokud je nalezena předpona verze s `Load` , algoritmus používá `GetKey` metodu k vrácení názvu konfigurace tajného názvu. Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.

Při implementaci tohoto přístupu:

1. Verze aplikace zadaná v souboru projektu aplikace V následujícím příkladu je verze aplikace nastavená na `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Zkontrolujte, zda je `<UserSecretsId>` v souboru projektu aplikace přítomna vlastnost, kde `{GUID}` je uživatelem zadaný identifikátor GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po spuštění aplikace se načtou tajné klíče trezoru klíčů. Řetězcová tajná `5000-AppSecret` verze pro je shodná s verzí aplikace zadanou v souboru projektu aplikace ( `5.0.0.0` ).

1. Verze `5000` (s pomlčkou) je z názvu klíče odstraněna. V celé aplikaci čtení konfigurace pomocí klíče `AppSecret` načte tajnou hodnotu.

1. Pokud se v souboru projektu změní verze aplikace na `5.1.0.0` a aplikace se znovu spustí, vrátí tajná hodnota `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.

> [!NOTE]
> Můžete také zadat vlastní <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementaci do <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.

Při čtení ze zdroje konfigurace, který umožňuje klíčům, aby obsahoval `:` oddělovače dvojtečky (), je k odlišení klíčů, které tvoří pole ( `:0:` ,,), použit numerický segment klíče `:1:` &hellip; `:{n}:` . Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku. Přístup popsaný v tomto tématu používá `--` pro hierarchické hodnoty (oddíly) dvojité pomlčky () jako oddělovač. Klíče pole jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON. V poli jsou definovány dva literály objektů, `WriteTo` které odrážejí dvě Serilog *jímky*, které popisují umístění pro výstup protokolování:

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

Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s použitím dvojité čárky ( `--` ) Notation a číselných segmentů:

| Klíč | Hodnota |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Znovu načíst tajné kódy

Tajné kódy jsou ukládány do mezipaměti `IConfigurationRoot.Reload()` , dokud není volána. V případě vypršení platnosti, zakázání a aktualizovaných tajných klíčů v trezoru klíčů se aplikace nedodržuje, dokud `Reload` se neprovede.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Zakázané a neprošlé tajné klíče

Zakázané a neprošlé tajné klíče vyvolají <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.

## <a name="troubleshoot"></a>Řešení potíží

Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva. Následující podmínky zabrání načtení konfigurace:

* Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.
* Trezor klíčů neexistuje v Azure Key Vault.
* Aplikace nemá autorizaci pro přístup k trezoru klíčů.
* Zásady přístupu nezahrnují `Get` oprávnění a `List` .
* V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.
* Aplikace má nesprávný název trezoru klíčů ( `KeyVaultName` ), ID aplikace Azure AD () `AzureADApplicationId` nebo kryptografický otisk certifikátu Azure AD ( `AzureADCertThumbprint` ).
* Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.
* Když přidáte zásady přístupu pro aplikaci do trezoru klíčů, zásada se vytvořila, ale tlačítko **Uložit** nebylo vybrané v uživatelském rozhraní **zásad přístupu** .

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentace Key Vault](/azure/key-vault/)
* [Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient – třída](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET](/azure/key-vault/quick-create-net)
* [Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento dokument vysvětluje, jak pomocí poskytovatele konfigurace [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z Azure Key Vault tajných klíčů. Azure Key Vault je cloudová služba, která pomáhá chránit kryptografické klíče a tajné klíče používané aplikacemi a službami. Mezi běžné scénáře použití Azure Key Vault s ASP.NET Core aplikacemi patří:

* Řízení přístupu k citlivým datům konfigurace.
* Splnění požadavku na Standard FIPS 140-2 úrovně 2: ověřované moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Balíčky

Přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Ukázková aplikace

Ukázková aplikace se spustí v jednom ze dvou režimů určených `#define` příkazem v horní části souboru *program.cs* :

* `Certificate`: Demonstruje použití Azure Key Vault ID klienta a certifikátu X. 509 pro přístup k tajným klíčům uloženým v Azure Key Vault. Tuto verzi ukázky můžete spustit z libovolného umístění, nasadit do Azure App Service nebo libovolného hostitele, který podporuje aplikaci ASP.NET Core.
* `Managed`: Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření, že aplikace Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace. Při ověřování identity pomocí spravovaných identit se nevyžaduje ID a heslo aplikace služby Azure AD (tajný klíč klienta). `Managed`Verze ukázky musí být nasazená do Azure. Postupujte podle pokynů v části [použití spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) .

Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktiv preprocesoru ( `#define` ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Tajné úložiště ve vývojovém prostředí

Místně nastavte tajné klíče pomocí [nástroje Správce tajných](xref:security/app-secrets)klíčů. Když je ukázková aplikace spuštěná na místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště správce tajných klíčů.

Nástroj Správce tajných klíčů vyžaduje `<UserSecretsId>` vlastnost v souboru projektu aplikace. Nastavte hodnotu vlastnosti ( `{GUID}` ) na libovolný jedinečný identifikátor GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Tajné kódy jsou vytvořeny jako páry název-hodnota. Hierarchické hodnoty (konfigurační oddíly) používají `:` jako oddělovač v [ASP.NET Core názvů konfiguračních](xref:fundamentals/configuration/index) klíčů středník (dvojtečku).

Správce tajného kódu se používá z příkazového prostředí otevřeného pro [kořen obsahu](xref:fundamentals/index#content-root)projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Spusťte následující příkazy v příkazovém prostředí z [kořenu obsahu](xref:fundamentals/index#content-root) projektu pro nastavení tajných kódů pro ukázkovou aplikaci:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Pokud jsou tyto tajné klíče uložené v Azure Key Vault v [tajném úložišti v produkčním prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu, `_dev` přípona se změní na `_prod` . Přípona poskytuje vizuální hromádku ve výstupu aplikace, která označuje zdroj hodnot konfigurace.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajné úložiště v produkčním prostředí pomocí Azure Key Vault

Pokyny poskytované [rychlým startem: nastavení a načtení tajného klíče z Azure Key Vault pomocí](/azure/key-vault/quick-create-cli) rozhraní příkazového řádku Azure najdete zde shrnuté pro vytvoření Azure Key Vault a ukládání tajných kódů používaných ukázkovou aplikací. Další podrobnosti najdete v tématu.

1. Otevřete Azure Cloud Shell pomocí jedné z následujících metod v [Azure Portal](https://portal.azure.com/):

   * Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. V textovém poli použijte hledaný řetězec "Azure CLI".
   * Otevřete Cloud Shell v prohlížeči pomocí tlačítka pro **spuštění Cloud Shell** .
   * V nabídce v pravém horním rohu Azure Portal vyberte tlačítko **Cloud Shell** .

   Další informace najdete v tématu [Azure CLI](/cli/azure/) a [Přehled Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pokud jste to ještě neověřili, přihlaste se pomocí `az login` příkazu.

1. Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro novou skupinu prostředků a `{LOCATION}` oblast Azure (Datacenter):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte Trezor klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název nového trezoru klíčů a `{LOCATION}` oblast Azure (Datacenter):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte tajné klíče v trezoru klíčů jako páry název-hodnota.

   Názvy tajných kódů Azure Key Vault jsou omezené na alfanumerické znaky a pomlčky. Hierarchické hodnoty (konfigurační oddíly) používají `--` jako oddělovač (dvě pomlčky). Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v tajných názvech trezoru klíčů. Proto se při načtení tajných klíčů do konfigurace aplikace použijí dvě pomlčky, které jsou v případě dvojtečky zahozeny.

   Následující tajné klíče jsou pro použití s ukázkovou aplikací. Hodnoty zahrnují `_prod` příponu pro jejich rozlišení od `_dev` hodnot přípony načtených ve vývojovém prostředí od uživatelských tajných kódů. Nahraďte `{KEY VAULT NAME}` názvem trezoru klíčů, který jste vytvořili v předchozím kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Použití ID aplikace a certifikátu X. 509 pro aplikace hostované mimo Azure

Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používala Azure Active Directory ID aplikace a certifikát X. 509 k ověřování u trezoru klíčů, **když je aplikace hostovaná mimo Azure**. Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> I když se pro aplikace hostované v Azure podporuje certifikát s ID aplikace a X. 509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) . Spravované identity nevyžadují uložení certifikátu v aplikaci nebo ve vývojovém prostředí.

Ukázková aplikace používá ID aplikace a certifikát X. 509, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Certificate` .

1. Vytvořte certifikát PKCS # 12 (*. pfx*). Mezi možnosti vytváření certifikátů patří [Makecert ve Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).
1. Nainstalujte certifikát do osobního úložiště certifikátů aktuálního uživatele. Označení klíče jako exportovatelných je volitelné. Poznamenejte si kryptografický otisk certifikátu, který se používá později v tomto procesu.
1. Exportujte certifikát PKCS # 12 (*. pfx*) jako certifikát kódovaný v kódování DER (*. cer*).
1. Zaregistrujte aplikaci ve službě Azure AD (**Registrace aplikací**).
1. Nahrajte certifikát s kódováním DER (*. cer*) do Azure AD:
   1. Vyberte aplikaci ve službě Azure AD.
   1. Přejděte na **certifikáty & tajných**kódů.
   1. Vyberte **Odeslat certifikát** pro nahrání certifikátu, který obsahuje veřejný klíč. Je přijatelný certifikát *. cer*, *. pem*nebo *. CRT* .
1. V souboru *appSettings. JSON* aplikace uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu.
1. V Azure Portal přejděte na **trezory klíčů** .
1. [V části provozní prostředí s Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) vyberte Trezor klíčů, který jste vytvořili v tajném úložišti.
1. Vyberte **Zásady přístupu**.
1. Vyberte **Přidat zásady přístupu**.
1. Otevřete **oprávnění tajného klíče** a poskytněte aplikaci oprávnění **získat** a vytvořit **seznam** .
1. Vyberte **Vybrat objekt zabezpečení** a vyberte zaregistrovanou aplikaci podle názvu. Vyberte tlačítko **Vybrat** .
1. Vyberte **OK**.
1. Vyberte **Uložit**.
1. Nasaďte aplikaci.

`Certificate`Ukázková aplikace získá své konfigurační hodnoty ze `IConfigurationRoot` stejného názvu jako název tajného kódu:

* Nehierarchické hodnoty: hodnota pro `SecretName` je získána s `config["SecretName"]` .
* Hierarchické hodnoty (oddíly): použijte `:` notaci (dvojtečku) nebo `GetSection` metodu rozšíření. K získání hodnoty konfigurace použijte některý z těchto přístupů:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certifikát X. 509 spravuje operační systém. Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> hodnoty zadané v souboru *appSettings. JSON* :

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Příklady hodnot:

* Název trezoru klíčů:`contosovault`
* ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`
* Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appSettings. JSON*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty. Ve vývojovém prostředí se tajné hodnoty načítají s `_dev` příponou. V produkčním prostředí se hodnoty načítají s `_prod` příponou.

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravovaných identit pro prostředky Azure

**Aplikace nasazená do Azure** může využít výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje Azure Key Vault aplikacím ověřování pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a heslo nebo tajný klíč klienta) uložené v aplikaci.

Ukázková aplikace používá spravované identity pro prostředky Azure, pokud `#define` je příkaz v horní části souboru *program.cs* nastavený na `Managed` .

Do souboru *appSettings. JSON* aplikace zadejte název trezoru. Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč klienta), pokud je nastavená `Managed` verze, takže můžete tyto položky konfigurace ignorovat. Aplikace je nasazená do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appSettings. JSON* .

Nasaďte ukázkovou aplikaci do Azure App Service.

Aplikace nasazená do Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD. Získejte ID objektu z nasazení pro použití v následujícím příkazu. ID objektu se zobrazí v Azure Portal na **Identity** panelu App Service.

Pomocí rozhraní příkazového řádku Azure a ID objektu aplikace poskytněte aplikaci `list` a `get` oprávnění pro přístup k trezoru klíčů:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal.

Ukázková aplikace:

* Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce. Pokud není zadaný připojovací řetězec, pokusí se zprostředkovatel získat přístupový token ze spravovaných identit pro prostředky Azure.
* Vytvoří <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se nový s `AzureServiceTokenProvider` voláním tokenu instance.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Instance se používá s výchozí implementací <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která načte všechny tajné hodnoty a nahradí dvojité spojovníky ( `--` ) dvojtečkami ( `:` ) v názvech klíčů.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Ukázková hodnota názvu trezoru klíčů:`contosovault`
    
*appSettings. JSON*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Když aplikaci spouštíte, zobrazí se na webové stránce načtené tajné hodnoty. Ve vývojovém prostředí mají tajné hodnoty příponu, `_dev` protože jsou poskytovány uživatelskými tajemstvími. V produkčním prostředí se hodnoty načítají s `_prod` příponou, protože jsou k dispozici v Azure Key Vault.

Pokud se zobrazí `Access denied` Chyba, zkontrolujte, že je aplikace zaregistrovaná ve službě Azure AD a že poskytuje přístup k trezoru klíčů. Potvrďte, že jste službu restartovali v Azure.

Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete v tématu [vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Použít předponu názvu klíče

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá implementaci <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , která umožňuje řídit, jak se tajné klíče trezoru klíčů převádějí do konfiguračních klíčů. Například můžete implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace. To vám umožní například načíst tajné kódy na základě verze aplikace.

> [!WARNING]
> Nepoužívejte předpony pro tajné klíče trezoru klíčů k umístění tajných kódů pro několik aplikací do stejného trezoru klíčů nebo k umístění tajných kódů prostředí (například *vývoj* a *produkčních* tajemství) do stejného trezoru. Doporučujeme, aby různé aplikace a vývojové a provozní prostředí používaly samostatné trezory klíčů k izolaci aplikačních prostředí na nejvyšší úrovni zabezpečení.

V následujícím příkladu se v trezoru klíčů vytvoří tajný kód (a pomocí nástroje Správce tajných klíčů pro vývojové prostředí) pro `5000-AppSecret` (období nejsou v tajných názvech trezoru klíčů povolená). Tento tajný klíč představuje tajný klíč aplikace pro 5.0.0.0 verze aplikace. V případě jiné verze aplikace se 5.1.0.0 do trezoru klíčů přidá tajný kód (a pomocí nástroje Správce tajného klíče) pro `5100-AppSecret` . Každá verze aplikace načte svou tajnou hodnotu do své konfigurace jako `AppSecret` a při načtení tajného kódu vymění verzi.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>je volána s vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementace reaguje na prefixy verze tajných kódů, aby se do konfigurace načetl správný tajný kód:

* `Load`Načte tajný klíč, pokud jeho název začíná předponou. Další tajné kódy nejsou načteny.
* `GetKey`:
  * Odebere předponu z názvu tajného kódu.
  * Nahradí dvě pomlčky v libovolném názvu pomocí `KeyDelimiter` , což je oddělovač použitý v konfiguraci (obvykle dvojtečka). Azure Key Vault v tajných názvech nepovoluje dvojtečku.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Metoda je volána algoritmem poskytovatele, který prochází tajné klíče trezoru, aby bylo možné najít ty, které mají předponu verze. Pokud je nalezena předpona verze s `Load` , algoritmus používá `GetKey` metodu k vrácení názvu konfigurace tajného názvu. Oddělí předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název-hodnota konfigurace aplikace.

Při implementaci tohoto přístupu:

1. Verze aplikace zadaná v souboru projektu aplikace V následujícím příkladu je verze aplikace nastavená na `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Zkontrolujte, zda je `<UserSecretsId>` v souboru projektu aplikace přítomna vlastnost, kde `{GUID}` je uživatelem zadaný identifikátor GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Následující tajná klíčová okna uložte místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Tajné kódy se ukládají v Azure Key Vault pomocí následujících příkazů Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Po spuštění aplikace se načtou tajné klíče trezoru klíčů. Řetězcová tajná `5000-AppSecret` verze pro je shodná s verzí aplikace zadanou v souboru projektu aplikace ( `5.0.0.0` ).

1. Verze `5000` (s pomlčkou) je z názvu klíče odstraněna. V celé aplikaci čtení konfigurace pomocí klíče `AppSecret` načte tajnou hodnotu.

1. Pokud se v souboru projektu změní verze aplikace na `5.1.0.0` a aplikace se znovu spustí, vrátí tajná hodnota `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.

> [!NOTE]
> Můžete také zadat vlastní <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementaci do <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Vlastní klient umožňuje v rámci aplikace sdílet jednu instanci klienta.

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

Zprostředkovatel je schopný číst konfigurační hodnoty do pole pro vazbu k poli POCO.

Při čtení ze zdroje konfigurace, který umožňuje klíčům, aby obsahoval `:` oddělovače dvojtečky (), je k odlišení klíčů, které tvoří pole ( `:0:` ,,), použit numerický segment klíče `:1:` &hellip; `:{n}:` . Další informace najdete v tématu [Konfigurace: vytvoření vazby pole ke třídě](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klíče Azure Key Vault nemůžou jako oddělovač použít dvojtečku. Přístup popsaný v tomto tématu používá `--` pro hierarchické hodnoty (oddíly) dvojité pomlčky () jako oddělovač. Klíče pole jsou uloženy v Azure Key Vault s dvojitými pomlčkami a segmenty číselných klíčů ( `--0--` , `--1--` , &hellip; `--{n}--` ).

Projděte si následující konfiguraci poskytovatele protokolování [Serilog](https://serilog.net/) poskytnutou souborem JSON. V poli jsou definovány dva literály objektů, `WriteTo` které odrážejí dvě Serilog *jímky*, které popisují umístění pro výstup protokolování:

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

Konfigurace zobrazená v předchozím souboru JSON je uložená v Azure Key Vault s použitím dvojité čárky ( `--` ) Notation a číselných segmentů:

| Klíč | Hodnota |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Znovu načíst tajné kódy

Tajné kódy jsou ukládány do mezipaměti `IConfigurationRoot.Reload()` , dokud není volána. V případě vypršení platnosti, zakázání a aktualizovaných tajných klíčů v trezoru klíčů se aplikace nedodržuje, dokud `Reload` se neprovede.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Zakázané a neprošlé tajné klíče

Zakázané a neprošlé tajné klíče vyvolají <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Chcete-li zabránit tomu, aby se aplikace vyvolala, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázané nebo neprošlé tajné klíče.

## <a name="troubleshoot"></a>Řešení potíží

Když se aplikaci nepovede načíst konfiguraci pomocí poskytovatele, do [ASP.NET Core infrastruktury protokolování](xref:fundamentals/logging/index)se zapíše chybová zpráva. Následující podmínky zabrání načtení konfigurace:

* Aplikace nebo certifikát nejsou správně nakonfigurované v Azure Active Directory.
* Trezor klíčů neexistuje v Azure Key Vault.
* Aplikace nemá autorizaci pro přístup k trezoru klíčů.
* Zásady přístupu nezahrnují `Get` oprávnění a `List` .
* V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela její platnost.
* Aplikace má nesprávný název trezoru klíčů ( `KeyVaultName` ), ID aplikace Azure AD () `AzureADApplicationId` nebo kryptografický otisk certifikátu Azure AD ( `AzureADCertThumbprint` ).
* Konfigurační klíč (Name) je v aplikaci v případě hodnoty, kterou se pokoušíte načíst, nesprávný.
* Když přidáte zásady přístupu pro aplikaci do trezoru klíčů, zásada se vytvořila, ale tlačítko **Uložit** nebylo vybrané v uživatelském rozhraní **zásad přístupu** .

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: dokumentace Key Vault](/azure/key-vault/)
* [Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient – třída](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET](/azure/key-vault/quick-create-net)
* [Kurz: použití Azure Key Vault s virtuálním počítačem Azure s Windows v .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

