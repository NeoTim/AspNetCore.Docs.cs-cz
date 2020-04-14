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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Zprostředkovatel konfigurace trezoru klíčů Azure v ASP.NET jádra

Podle [Andrew Stanton-Sestra](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Tento dokument vysvětluje, jak pomocí zprostředkovatele konfigurace [úložiště klíčů Microsoft Azure](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z tajných kódů úložiště klíčů Azure. Azure Key Vault je cloudová služba, která pomáhá při ochraně kryptografických klíčů a tajných kódů používaných aplikacemi a službami. Mezi běžné scénáře používání služby Azure Key Vault s aplikacemi ASP.NET Core patří:

* Řízení přístupu k citlivým konfiguračním datům.
* Splnění požadavku na fips 140-2 level 2 ověřené moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>Balíčky

Přidejte odkaz na balíček na balíček [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Ukázková aplikace

Ukázková aplikace běží v jednom ze `#define` dvou režimů určených příkazem v horní části *Program.cs* souboru:

* `Certificate`&ndash; Ukazuje použití ID klienta trezoru klíčů Azure a certifikátu X.509 pro přístup k tajným kódům uloženým v úložišti klíčů Azure. Tuto verzi ukázky lze spustit z libovolného umístění, nasazené do služby Azure App Service nebo libovolného hostitele schopného obsluhovat aplikaci ASP.NET Core.
* `Managed`&ndash; Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace. Při použití spravovaných identit k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) nejsou povinné. Verze `Managed` ukázky musí být nasazená do Azure. Postupujte podle pokynů v části [Použití spravovaných identit pro prostředky Azure.](#use-managed-identities-for-azure-resources)

Další informace o konfiguraci ukázkové aplikace pomocí`#define`direktiv preprocesoru ( ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Tajné úložiště ve vývojovém prostředí

Nastavte tajné klíče místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets). Když ukázková aplikace běží v místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště Správce tajných klíčů.

Nástroj Správce tajných `<UserSecretsId>` barev vyžaduje vlastnost v souboru projektu aplikace. Nastavte hodnotu`{GUID}`vlastnosti ( ) na libovolný jedinečný identifikátor GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Tajné klíče jsou vytvořeny jako dvojice název-hodnota. Hierarchické hodnoty (konfigurační oddíly) používají `:` (dvojtečku) jako oddělovač v názvech ASP.NET [konfiguračních klíčů Jádra.](xref:fundamentals/configuration/index)

Správce tajných barev se používá z příkazového prostředí otevřeného do `{SECRET VALUE}` [kořenového adresáře obsahu](xref:fundamentals/index#content-root)projektu , kde `{SECRET NAME}` je název a je hodnota:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Spusťte následující příkazy v příkazovém prostředí z [kořenového adresáře obsahu](xref:fundamentals/index#content-root) projektu a nastavte tajné kódy ukázkové aplikace:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Když jsou tyto tajné klíče uloženy v trezoru klíčů Azure v `_dev` [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault, přípona se změní na `_prod`. Přípona poskytuje vizuální podnět ve výstupu aplikace označující zdroj hodnot konfigurace.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajné úložiště v produkčním prostředí s trezorem klíčů Azure

Pokyny poskytnuté na [úvodním panelu: Nastavení a načtení tajného klíče z azure key vault pomocí tématu Azure CLI](/azure/key-vault/quick-create-cli) jsou shrnuty zde pro vytvoření trezoru klíčů Azure a ukládání tajných kódů používaných ukázkovou aplikací. Další podrobnosti naleznete v tématu.

1. Otevřete prostředí Azure Cloud pomocí některé z následujících metod na [webu Azure Portal](https://portal.azure.com/):

   * Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. Použijte vyhledávací řetězec "Azure CLI" v textovém poli.
   * Otevřete Cloud Shell ve svém prohlížeči pomocí tlačítka **Spustit cloudové prostředí.**
   * V nabídce v pravém horním rohu portálu Azure vyberte tlačítko **Cloud Shell.**

   Další informace najdete v [tématu Azure CLI](/cli/azure/) a [přehled Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pokud ještě nejste ověřeni, přihlaste se pomocí příkazu. `az login`

1. Vytvořte skupinu prostředků s `{RESOURCE GROUP NAME}` následujícím příkazem, kde je název `{LOCATION}` skupiny prostředků pro novou skupinu prostředků a je oblast Azure (datové centrum):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte trezor klíčů ve skupině prostředků pomocí `{KEY VAULT NAME}` následujícího příkazu, kde `{LOCATION}` je název pro nový trezor klíčů a je oblast Azure (datové centrum):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte tajné klíče v trezoru klíčů jako dvojice název-hodnota.

   Tajné názvy azure key vault jsou omezeny na alfanumerické znaky a pomlčky. Hierarchické hodnoty (konfigurační sekce) používají `--` (dvě pomlčky) jako oddělovač. Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET jádra](xref:fundamentals/configuration/index), nejsou v tajných názvech trezoru klíčů povoleny. Proto dvě pomlčky se používají a prohozeny pro dvojtečku při načtení tajných kódů do konfigurace aplikace.

   Následující tajné klíče jsou určeny pro použití s ukázkovou aplikací. Hodnoty zahrnují `_prod` příponu k jejich `_dev` odlišení od hodnot přípony načtených ve vývojovém prostředí od tajných kódů uživatelů. Nahraďte `{KEY VAULT NAME}` název trezoru klíčů, který jste vytvořili v předchozím kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Použití ID aplikace a certifikátu X.509 pro aplikace, které nejsou hostované v Azure

Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používaly ID aplikace Azure Active Directory a certifikát X.509 k ověření do trezoru **klíčů, když je aplikace hostovaná mimo Azure**. Další informace naleznete v [tématu O klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> I když je pro aplikace hostované v Azure podporované id aplikace a certifikát X.509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure.](#use-managed-identities-for-azure-resources) Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.

Ukázková aplikace používá id aplikace a certifikát X.509, `#define` pokud je příkaz v horní části *Program.cs* soubornastaven na `Certificate`.

1. Vytvořte certifikát archivu PKCS#12 (*.pfx).* Mezi možnosti vytváření certifikátů patří [MakeCert v systémech Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).
1. Nainstalujte certifikát do úložiště osobních certifikátů aktuálního uživatele. Označení klíče jako exportovatelného je volitelné. Všimněte si kryptografického otisku certifikátu, který se používá později v tomto procesu.
1. Exportujte certifikát Archivu PKCS#12 (*.pfx)* jako certifikát kódovaný der (*.cer*).
1. Zaregistrujte aplikaci pomocí Azure AD **(Registrace aplikací).**
1. Nahrajte certifikát kódovaný der (*.cer*) do Služby Azure AD:
   1. Vyberte aplikaci ve službě Azure AD.
   1. Přejděte na **tajné klíče & certifikáty**.
   1. Výběrem **možnosti Odeslat certifikát** nahrajete certifikát, který obsahuje veřejný klíč. Certifikát *.cer*, *.pem*nebo *.crt* je přijatelný.
1. Uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu do souboru *appsettings.json.*
1. Přejděte do **trezorů klíčů** na webu Azure Portal.
1. Vyberte trezor klíčů, který jste vytvořili v [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault.
1. Vyberte **Zásady přístupu**.
1. Vyberte **Přidat zásady přístupu**.
1. Otevřete **oprávnění Tajné a** poskytněte aplikaci oprávnění **Získat** a **Seznam.**
1. Vyberte **Vybrat hlavní objekt** a vyberte registrovanou aplikaci podle názvu. Vyberte tlačítko **Vybrat.**
1. Vyberte **OK**.
1. Vyberte **Uložit**.
1. Nasaďte aplikaci.

Ukázková `Certificate` aplikace získává své `IConfigurationRoot` hodnoty konfigurace ze stejného názvu jako tajný název:

* Nehierarchické hodnoty: Hodnota `SecretName` pro je `config["SecretName"]`získána pomocí .
* Hierarchické hodnoty (oddíly): Použijte `:` zápis `GetSection` (dvojtečka) nebo metodu rozšíření. Pomocí některého z těchto přístupů získáte hodnotu konfigurace:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certifikát X.509 je spravován os. Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> s hodnotami dodanými souborem *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Příklady hodnot:

* Název trezoru klíčů:`contosovault`
* ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`
* Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty. Ve vývojovém prostředí se tajné `_dev` hodnoty načítají s příponou. V produkčním prostředí se hodnoty `_prod` načítají s příponou.

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravovaných identit pro prostředky Azure

**Aplikace nasazená do Azure** může využívat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje aplikaci ověřovat pomocí Azure Key Vault pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a tajný klíč klienta) uložené v aplikaci.

Ukázková aplikace používá spravované identity `#define` pro prostředky Azure, když je `Managed`příkaz v horní části *Program.cs* souboru nastavenna .

Zadejte název úschovny do souboru *appsettings.json* aplikace. Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč `Managed` klienta) při nastavení na verzi, takže můžete tyto položky konfigurace ignorovat. Aplikace se nasadí do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appsettings.json.*

Nasaďte ukázkovou aplikaci do služby Azure App Service.

Aplikace nasazená ve službě Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD. Získejte ID objektu z nasazení pro použití v následujícím příkazu. ID objektu se zobrazí na portálu Azure na panelu **Identita služby** App Service.

Pomocí Azure CLI a ID objektu aplikace, `list` `get` poskytnout aplikaci a oprávnění pro přístup k trezoru klíčů:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Restartujte aplikaci** pomocí Azure CLI, PowerShellu nebo portálu Azure.

Ukázková aplikace:

* Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce. Když není k dispozici připojovací řetězec, poskytovatel se pokusí získat přístupový token ze spravovaných identit pro prostředky Azure.
* Nový <xref:Microsoft.Azure.KeyVault.KeyVaultClient> je vytvořen `AzureServiceTokenProvider` s token instance zpětného volání.
* Instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se používá s výchozí <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementací, která načte všechny`--`tajné hodnoty a`:`nahradí dvojité pomlčky ( ) dvojtečkami ( ) v názvech klíčů.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Příklad příklad názvu trezoru klíčů:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty. Ve vývojovém prostředí mají `_dev` tajné hodnoty příponu, protože jsou poskytovány tajnými kódy uživatelů. V produkčním prostředí se hodnoty `_prod` načítají s příponou, protože jsou poskytovány službou Azure Key Vault.

Pokud se `Access denied` zobrazí chyba, zkontrolujte, že aplikace je registrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů. Zkontrolujte, že jste službu v Azure restartovali.

Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete [v tématu Vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Možnosti konfigurace

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>může přijmout <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Vlastnost         | Popis |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>pro načítání hodnot. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>instance slouží k řízení tajnénačítání. |
| `ReloadInterval` | `Timespan`čekání mezi pokusy o dotazování trezoru klíčů pro změny. Výchozí hodnota `null` je (konfigurace není znovu načtena). |
| `Vault`          | Identifikátor URI trezoru klíčů. |

## <a name="use-a-key-name-prefix"></a>Použití předpony názvu klíče

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementaci aplikace , která umožňuje řídit, jak jsou tajné klíče trezoru klíčů převedeny na konfigurační klíče. Můžete například implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace. To umožňuje například načíst tajné klíče na základě verze aplikace.

> [!WARNING]
> Nepoužívejte předpony na tajných kódech trezoru klíčů k umístění tajných kódů pro více aplikací do stejného trezoru klíčů nebo k umístění environmentálních tajemství (například *ukvačování vývoje* versus *produkčních* tajemství) do stejného trezoru. Doporučujeme, aby různé aplikace a vývojová a produkční prostředí používaly samostatné trezory klíčů k oddělení prostředí aplikací pro nejvyšší úroveň zabezpečení.

V následujícím příkladu je v trezoru klíčů vytvořen tajný klíč (a použití `5000-AppSecret` nástroje Správce tajných klíčů pro vývojové prostředí) (období nejsou povolena v názvech tajných klíčů). Tento tajný klíč představuje tajný klíč aplikace pro verzi 5.0.0.0 aplikace. Pro jinou verzi aplikace, 5.1.0.0, je tajný klíč přidán do trezoru `5100-AppSecret`klíčů (a pomocí nástroje Správce tajných klíčů) pro . Každá verze aplikace načte svou verzi `AppSecret`tajné hodnoty do své konfigurace jako , stripping off verzi, jak se načte tajný klíč.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se nazývá s <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>vlastní :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Implementace <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na předpony verzí tajných kódů k načtení správného tajného klíče do konfigurace:

* `Load`načte tajný klíč, když jeho název začíná předponou. Ostatní tajemství nejsou nabitá.
* `GetKey`:
  * Odebere předponu z tajného názvu.
  * Nahradí dvě pomlčky v libovolném názvu `KeyDelimiter`s , což je oddělovač používaný v konfiguraci (obvykle dvojtečka). Azure Key Vault neumožňuje dvojtečku v tajných názvech.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Metoda `Load` je volána algoritmus zprostředkovatele, který itepraví přes tajné kódy trezoru najít ty, které mají předponu verze. Pokud je nalezena předpona verze `Load` `GetKey` s , algoritmus používá metodu k vrácení názvu konfigurace tajného názvu. Odstraní předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název názvu konfigurace aplikace.

Při provádění tohoto přístupu:

1. Verze aplikace zadaná v souboru projektu aplikace. V následujícím příkladu je verze aplikace `5.0.0.0`nastavena na :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Zkontrolujte, `<UserSecretsId>` zda je v souboru projektu `{GUID}` aplikace k dispozici vlastnost, kde je identifikátor GUID dodaný uživatelem:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Uložte následující tajné kódy místně pomocí [nástroje Správce tajných barev](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Tajné klíče se ukládají do úložiště klíčů Azure pomocí následujících příkazů Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Při spuštění aplikace jsou načteny tajné klíče trezoru klíčů. Tajný klíč `5000-AppSecret` řetězce pro je spárován s verzí aplikace zadanou v`5.0.0.0`souboru projektu aplikace ( ).

1. Verze `5000` (s pomlčkou) je odstraněna z názvu klíče. V celé aplikaci čtení `AppSecret` konfigurace s klíčem načte tajnou hodnotu.

1. Pokud se verze aplikace změní v souboru projektu a `5.1.0.0` aplikace se spustí `5.1.0.0_secret_value_dev` znovu, vrácená `5.1.0.0_secret_value_prod` tajná hodnota je ve vývojovém prostředí a v produkčním prostředí.

> [!NOTE]
> Můžete také poskytnout <xref:Microsoft.Azure.KeyVault.KeyVaultClient> vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementaci . Vlastní klient umožňuje sdílení jedné instance klienta v rámci aplikace.

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

Zprostředkovatel je schopen číst hodnoty konfigurace do pole pro vazbu na pole POCO.

Při čtení ze zdroje konfigurace, který`:`umožňuje klíčům obsahovat oddělovače dvojtečky ( ),`:0:`se `:1:` &hellip; `:{n}:`k rozlišení klíčů tvořících pole používá číselný segment klíče ( , , ). Další informace naleznete v [tématu Konfigurace: Vazba pole na třídu](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klíče Azure Key Vault nelze použít dvojtečku jako oddělovač. Přístup popsaný v tomto tématu používá`--`dvojité pomlčky ( ) jako oddělovač pro hierarchické hodnoty (oddíly). Klíče pole jsou uloženy v trezoru klíčů Azure`--0--`s `--1--` &hellip; `--{n}--`dvojitými pomlčkami a číselnými segmenty klíčů ( , , ).

Prohlédněte si následující konfiguraci zprostředkovatele protokolování [Serilog](https://serilog.net/) poskytovou souboru JSON. V `WriteTo` poli jsou definovány dva literály objektu, které odrážejí dvě *jímky*Serilog , které popisují cíle pro protokolování výstupu:

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

Konfigurace zobrazená v předchozím souboru JSON je uložena`--`v azure key vaultu pomocí dvojité pomlčky ( ) zápisu a číselných segmentů:

| Klíč | Hodnota |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Znovu načíst tajné kódy

Tajné klíče jsou `IConfigurationRoot.Reload()` ukládány do mezipaměti, dokud není volána. Vypršela platnost, zakázáno a aktualizované tajné klíče v trezoru klíčů nejsou respektovány aplikací, dokud `Reload` není proveden.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Zakázané a prošlé tajné klíče

Zakázané a vypršela <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>platnost tajných kódů hodit . Chcete-li zabránit vyvolání aplikace, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázaný tajný klíč nebo tajný klíč, jehož platnost vypršela.

## <a name="troubleshoot"></a>Řešení potíží

Když se aplikaci nepodaří načíst konfiguraci pomocí zprostředkovatele, zapíše se chybová zpráva do [infrastruktury ASP.NET core logging](xref:fundamentals/logging/index). Následující podmínky zabrání načtení konfigurace:

* Aplikace nebo certifikát není správně nakonfigurovaný ve službě Azure Active Directory.
* Trezor klíčů neexistuje v azure key vault.
* Aplikace nemá oprávnění k přístupu do trezoru klíčů.
* Zásady přístupu `Get` neobsahuje `List` a oprávnění.
* V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela jejich platnost.
* Aplikace má nesprávný název`KeyVaultName`trezoru klíčů ( ),`AzureADApplicationId`ID aplikace Azure AD ( ) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).
* Konfigurační klíč (název) je v aplikaci nesprávný pro hodnotu, kterou se pokoušíte načíst.
* Při přidávání zásad přístupu pro aplikaci do trezoru klíčů byla zásada vytvořena, ale tlačítko **Uložit** nebylo v umělou iniciálním nastavení **zásad přístupu** vybráno.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Trezor klíčů](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Dokumentace trezoru klíčů](/azure/key-vault/)
* [Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Třída KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET](/azure/key-vault/quick-create-net)
* [Kurz: Jak používat Azure Key Vault s Azure Windows Virtual Machine v rozhraní .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento dokument vysvětluje, jak pomocí zprostředkovatele konfigurace [úložiště klíčů Microsoft Azure](https://azure.microsoft.com/services/key-vault/) načíst hodnoty konfigurace aplikace z tajných kódů úložiště klíčů Azure. Azure Key Vault je cloudová služba, která pomáhá při ochraně kryptografických klíčů a tajných kódů používaných aplikacemi a službami. Mezi běžné scénáře používání služby Azure Key Vault s aplikacemi ASP.NET Core patří:

* Řízení přístupu k citlivým konfiguračním datům.
* Splnění požadavku na fips 140-2 level 2 ověřené moduly hardwarového zabezpečení (HSM) při ukládání konfiguračních dat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>Balíčky

Přidejte odkaz na balíček na balíček [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Ukázková aplikace

Ukázková aplikace běží v jednom ze `#define` dvou režimů určených příkazem v horní části *Program.cs* souboru:

* `Certificate`&ndash; Ukazuje použití ID klienta trezoru klíčů Azure a certifikátu X.509 pro přístup k tajným kódům uloženým v úložišti klíčů Azure. Tuto verzi ukázky lze spustit z libovolného umístění, nasazené do služby Azure App Service nebo libovolného hostitele schopného obsluhovat aplikaci ASP.NET Core.
* `Managed`&ndash; Ukazuje, jak používat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do Azure Key Vault s ověřováním Azure AD bez přihlašovacích údajů uložených v kódu nebo konfiguraci aplikace. Při použití spravovaných identit k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) nejsou povinné. Verze `Managed` ukázky musí být nasazená do Azure. Postupujte podle pokynů v části [Použití spravovaných identit pro prostředky Azure.](#use-managed-identities-for-azure-resources)

Další informace o konfiguraci ukázkové aplikace pomocí`#define`direktiv preprocesoru ( ), naleznete v tématu <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Tajné úložiště ve vývojovém prostředí

Nastavte tajné klíče místně pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets). Když ukázková aplikace běží v místním počítači ve vývojovém prostředí, tajné klíče se načtou z místního úložiště Správce tajných klíčů.

Nástroj Správce tajných `<UserSecretsId>` barev vyžaduje vlastnost v souboru projektu aplikace. Nastavte hodnotu`{GUID}`vlastnosti ( ) na libovolný jedinečný identifikátor GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Tajné klíče jsou vytvořeny jako dvojice název-hodnota. Hierarchické hodnoty (konfigurační oddíly) používají `:` (dvojtečku) jako oddělovač v názvech ASP.NET [konfiguračních klíčů Jádra.](xref:fundamentals/configuration/index)

Správce tajných barev se používá z příkazového prostředí otevřeného do `{SECRET VALUE}` [kořenového adresáře obsahu](xref:fundamentals/index#content-root)projektu , kde `{SECRET NAME}` je název a je hodnota:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Spusťte následující příkazy v příkazovém prostředí z [kořenového adresáře obsahu](xref:fundamentals/index#content-root) projektu a nastavte tajné kódy ukázkové aplikace:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Když jsou tyto tajné klíče uloženy v trezoru klíčů Azure v `_dev` [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault, přípona se změní na `_prod`. Přípona poskytuje vizuální podnět ve výstupu aplikace označující zdroj hodnot konfigurace.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Tajné úložiště v produkčním prostředí s trezorem klíčů Azure

Pokyny poskytnuté na [úvodním panelu: Nastavení a načtení tajného klíče z azure key vault pomocí tématu Azure CLI](/azure/key-vault/quick-create-cli) jsou shrnuty zde pro vytvoření trezoru klíčů Azure a ukládání tajných kódů používaných ukázkovou aplikací. Další podrobnosti naleznete v tématu.

1. Otevřete prostředí Azure Cloud pomocí některé z následujících metod na [webu Azure Portal](https://portal.azure.com/):

   * Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. Použijte vyhledávací řetězec "Azure CLI" v textovém poli.
   * Otevřete Cloud Shell ve svém prohlížeči pomocí tlačítka **Spustit cloudové prostředí.**
   * V nabídce v pravém horním rohu portálu Azure vyberte tlačítko **Cloud Shell.**

   Další informace najdete v [tématu Azure CLI](/cli/azure/) a [přehled Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pokud ještě nejste ověřeni, přihlaste se pomocí příkazu. `az login`

1. Vytvořte skupinu prostředků s `{RESOURCE GROUP NAME}` následujícím příkazem, kde je název `{LOCATION}` skupiny prostředků pro novou skupinu prostředků a je oblast Azure (datové centrum):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte trezor klíčů ve skupině prostředků pomocí `{KEY VAULT NAME}` následujícího příkazu, kde `{LOCATION}` je název pro nový trezor klíčů a je oblast Azure (datové centrum):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvořte tajné klíče v trezoru klíčů jako dvojice název-hodnota.

   Tajné názvy azure key vault jsou omezeny na alfanumerické znaky a pomlčky. Hierarchické hodnoty (konfigurační sekce) používají `--` (dvě pomlčky) jako oddělovač. Dvojtečky, které se obvykle používají k vymezení oddílu z podklíče v [konfiguraci ASP.NET jádra](xref:fundamentals/configuration/index), nejsou v tajných názvech trezoru klíčů povoleny. Proto dvě pomlčky se používají a prohozeny pro dvojtečku při načtení tajných kódů do konfigurace aplikace.

   Následující tajné klíče jsou určeny pro použití s ukázkovou aplikací. Hodnoty zahrnují `_prod` příponu k jejich `_dev` odlišení od hodnot přípony načtených ve vývojovém prostředí od tajných kódů uživatelů. Nahraďte `{KEY VAULT NAME}` název trezoru klíčů, který jste vytvořili v předchozím kroku:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Použití ID aplikace a certifikátu X.509 pro aplikace, které nejsou hostované v Azure

Nakonfigurujte Azure AD, Azure Key Vault a aplikaci tak, aby používaly ID aplikace Azure Active Directory a certifikát X.509 k ověření do trezoru **klíčů, když je aplikace hostovaná mimo Azure**. Další informace naleznete v [tématu O klíčích, tajných klíčích a certifikátech](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> I když je pro aplikace hostované v Azure podporované id aplikace a certifikát X.509, doporučujeme při hostování aplikace v Azure používat [spravované identity pro prostředky Azure.](#use-managed-identities-for-azure-resources) Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.

Ukázková aplikace používá id aplikace a certifikát X.509, `#define` pokud je příkaz v horní části *Program.cs* soubornastaven na `Certificate`.

1. Vytvořte certifikát archivu PKCS#12 (*.pfx).* Mezi možnosti vytváření certifikátů patří [MakeCert v systémech Windows](/windows/desktop/seccrypto/makecert) a [OpenSSL](https://www.openssl.org/).
1. Nainstalujte certifikát do úložiště osobních certifikátů aktuálního uživatele. Označení klíče jako exportovatelného je volitelné. Všimněte si kryptografického otisku certifikátu, který se používá později v tomto procesu.
1. Exportujte certifikát Archivu PKCS#12 (*.pfx)* jako certifikát kódovaný der (*.cer*).
1. Zaregistrujte aplikaci pomocí Azure AD **(Registrace aplikací).**
1. Nahrajte certifikát kódovaný der (*.cer*) do Služby Azure AD:
   1. Vyberte aplikaci ve službě Azure AD.
   1. Přejděte na **tajné klíče & certifikáty**.
   1. Výběrem **možnosti Odeslat certifikát** nahrajete certifikát, který obsahuje veřejný klíč. Certifikát *.cer*, *.pem*nebo *.crt* je přijatelný.
1. Uložte název trezoru klíčů, ID aplikace a kryptografický otisk certifikátu do souboru *appsettings.json.*
1. Přejděte do **trezorů klíčů** na webu Azure Portal.
1. Vyberte trezor klíčů, který jste vytvořili v [tajném úložišti v produkčním prostředí s](#secret-storage-in-the-production-environment-with-azure-key-vault) částí Azure Key Vault.
1. Vyberte **Zásady přístupu**.
1. Vyberte **Přidat zásady přístupu**.
1. Otevřete **oprávnění Tajné a** poskytněte aplikaci oprávnění **Získat** a **Seznam.**
1. Vyberte **Vybrat hlavní objekt** a vyberte registrovanou aplikaci podle názvu. Vyberte tlačítko **Vybrat.**
1. Vyberte **OK**.
1. Vyberte **Uložit**.
1. Nasaďte aplikaci.

Ukázková `Certificate` aplikace získává své `IConfigurationRoot` hodnoty konfigurace ze stejného názvu jako tajný název:

* Nehierarchické hodnoty: Hodnota `SecretName` pro je `config["SecretName"]`získána pomocí .
* Hierarchické hodnoty (oddíly): Použijte `:` zápis `GetSection` (dvojtečka) nebo metodu rozšíření. Pomocí některého z těchto přístupů získáte hodnotu konfigurace:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Certifikát X.509 je spravován os. Aplikace volá <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> s hodnotami dodanými souborem *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Příklady hodnot:

* Název trezoru klíčů:`contosovault`
* ID aplikace:`627e911e-43cc-61d4-992e-12db9c81b413`
* Kryptografický otisk certifikátu:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty. Ve vývojovém prostředí se tajné `_dev` hodnoty načítají s příponou. V produkčním prostředí se hodnoty `_prod` načítají s příponou.

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravovaných identit pro prostředky Azure

**Aplikace nasazená do Azure** může využívat [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), což umožňuje aplikaci ověřovat pomocí Azure Key Vault pomocí ověřování Azure AD bez přihlašovacích údajů (ID aplikace a tajný klíč klienta) uložené v aplikaci.

Ukázková aplikace používá spravované identity `#define` pro prostředky Azure, když je `Managed`příkaz v horní části *Program.cs* souboru nastavenna .

Zadejte název úschovny do souboru *appsettings.json* aplikace. Ukázková aplikace nevyžaduje ID aplikace a heslo (tajný klíč `Managed` klienta) při nastavení na verzi, takže můžete tyto položky konfigurace ignorovat. Aplikace se nasadí do Azure a Azure ověřuje aplikaci pro přístup k Azure Key Vault jenom pomocí názvu trezoru uloženého v souboru *appsettings.json.*

Nasaďte ukázkovou aplikaci do služby Azure App Service.

Aplikace nasazená ve službě Azure App Service se při vytvoření služby automaticky zaregistruje ve službě Azure AD. Získejte ID objektu z nasazení pro použití v následujícím příkazu. ID objektu se zobrazí na portálu Azure na panelu **Identita služby** App Service.

Pomocí Azure CLI a ID objektu aplikace, `list` `get` poskytnout aplikaci a oprávnění pro přístup k trezoru klíčů:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Restartujte aplikaci** pomocí Azure CLI, PowerShellu nebo portálu Azure.

Ukázková aplikace:

* Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovacího řetězce. Když není k dispozici připojovací řetězec, poskytovatel se pokusí získat přístupový token ze spravovaných identit pro prostředky Azure.
* Nový <xref:Microsoft.Azure.KeyVault.KeyVaultClient> je vytvořen `AzureServiceTokenProvider` s token instance zpětného volání.
* Instance <xref:Microsoft.Azure.KeyVault.KeyVaultClient> se používá s výchozí <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementací, která načte všechny`--`tajné hodnoty a`:`nahradí dvojité pomlčky ( ) dvojtečkami ( ) v názvech klíčů.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Příklad příklad názvu trezoru klíčů:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Při spuštění aplikace se na webové stránce zobrazí načtené tajné hodnoty. Ve vývojovém prostředí mají `_dev` tajné hodnoty příponu, protože jsou poskytovány tajnými kódy uživatelů. V produkčním prostředí se hodnoty `_prod` načítají s příponou, protože jsou poskytovány službou Azure Key Vault.

Pokud se `Access denied` zobrazí chyba, zkontrolujte, že aplikace je registrovaná ve službě Azure AD a poskytuje přístup k trezoru klíčů. Zkontrolujte, že jste službu v Azure restartovali.

Informace o používání zprostředkovatele se spravovanou identitou a kanálem Azure DevOps najdete [v tématu Vytvoření připojení služby Azure Resource Manager k virtuálnímu počítači s identitou spravované služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Použití předpony názvu klíče

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>poskytuje přetížení, které přijímá <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>implementaci aplikace , která umožňuje řídit, jak jsou tajné klíče trezoru klíčů převedeny na konfigurační klíče. Můžete například implementovat rozhraní pro načtení tajných hodnot na základě hodnoty předpony, kterou zadáte při spuštění aplikace. To umožňuje například načíst tajné klíče na základě verze aplikace.

> [!WARNING]
> Nepoužívejte předpony na tajných kódech trezoru klíčů k umístění tajných kódů pro více aplikací do stejného trezoru klíčů nebo k umístění environmentálních tajemství (například *ukvačování vývoje* versus *produkčních* tajemství) do stejného trezoru. Doporučujeme, aby různé aplikace a vývojová a produkční prostředí používaly samostatné trezory klíčů k oddělení prostředí aplikací pro nejvyšší úroveň zabezpečení.

V následujícím příkladu je v trezoru klíčů vytvořen tajný klíč (a použití `5000-AppSecret` nástroje Správce tajných klíčů pro vývojové prostředí) (období nejsou povolena v názvech tajných klíčů). Tento tajný klíč představuje tajný klíč aplikace pro verzi 5.0.0.0 aplikace. Pro jinou verzi aplikace, 5.1.0.0, je tajný klíč přidán do trezoru `5100-AppSecret`klíčů (a pomocí nástroje Správce tajných klíčů) pro . Každá verze aplikace načte svou verzi `AppSecret`tajné hodnoty do své konfigurace jako , stripping off verzi, jak se načte tajný klíč.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>se nazývá s <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>vlastní :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Implementace <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reaguje na předpony verzí tajných kódů k načtení správného tajného klíče do konfigurace:

* `Load`načte tajný klíč, když jeho název začíná předponou. Ostatní tajemství nejsou nabitá.
* `GetKey`:
  * Odebere předponu z tajného názvu.
  * Nahradí dvě pomlčky v libovolném názvu `KeyDelimiter`s , což je oddělovač používaný v konfiguraci (obvykle dvojtečka). Azure Key Vault neumožňuje dvojtečku v tajných názvech.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Metoda `Load` je volána algoritmus zprostředkovatele, který itepraví přes tajné kódy trezoru najít ty, které mají předponu verze. Pokud je nalezena předpona verze `Load` `GetKey` s , algoritmus používá metodu k vrácení názvu konfigurace tajného názvu. Odstraní předponu verze z názvu tajného klíče a vrátí zbytek tajného názvu pro načtení do dvojice název názvu konfigurace aplikace.

Při provádění tohoto přístupu:

1. Verze aplikace zadaná v souboru projektu aplikace. V následujícím příkladu je verze aplikace `5.0.0.0`nastavena na :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Zkontrolujte, `<UserSecretsId>` zda je v souboru projektu `{GUID}` aplikace k dispozici vlastnost, kde je identifikátor GUID dodaný uživatelem:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Uložte následující tajné kódy místně pomocí [nástroje Správce tajných barev](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Tajné klíče se ukládají do úložiště klíčů Azure pomocí následujících příkazů Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Při spuštění aplikace jsou načteny tajné klíče trezoru klíčů. Tajný klíč `5000-AppSecret` řetězce pro je spárován s verzí aplikace zadanou v`5.0.0.0`souboru projektu aplikace ( ).

1. Verze `5000` (s pomlčkou) je odstraněna z názvu klíče. V celé aplikaci čtení `AppSecret` konfigurace s klíčem načte tajnou hodnotu.

1. Pokud se verze aplikace změní v souboru projektu a `5.1.0.0` aplikace se spustí `5.1.0.0_secret_value_dev` znovu, vrácená `5.1.0.0_secret_value_prod` tajná hodnota je ve vývojovém prostředí a v produkčním prostředí.

> [!NOTE]
> Můžete také poskytnout <xref:Microsoft.Azure.KeyVault.KeyVaultClient> vlastní <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>implementaci . Vlastní klient umožňuje sdílení jedné instance klienta v rámci aplikace.

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

Zprostředkovatel je schopen číst hodnoty konfigurace do pole pro vazbu na pole POCO.

Při čtení ze zdroje konfigurace, který`:`umožňuje klíčům obsahovat oddělovače dvojtečky ( ),`:0:`se `:1:` &hellip; `:{n}:`k rozlišení klíčů tvořících pole používá číselný segment klíče ( , , ). Další informace naleznete v [tématu Konfigurace: Vazba pole na třídu](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Klíče Azure Key Vault nelze použít dvojtečku jako oddělovač. Přístup popsaný v tomto tématu používá`--`dvojité pomlčky ( ) jako oddělovač pro hierarchické hodnoty (oddíly). Klíče pole jsou uloženy v trezoru klíčů Azure`--0--`s `--1--` &hellip; `--{n}--`dvojitými pomlčkami a číselnými segmenty klíčů ( , , ).

Prohlédněte si následující konfiguraci zprostředkovatele protokolování [Serilog](https://serilog.net/) poskytovou souboru JSON. V `WriteTo` poli jsou definovány dva literály objektu, které odrážejí dvě *jímky*Serilog , které popisují cíle pro protokolování výstupu:

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

Konfigurace zobrazená v předchozím souboru JSON je uložena`--`v azure key vaultu pomocí dvojité pomlčky ( ) zápisu a číselných segmentů:

| Klíč | Hodnota |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Znovu načíst tajné kódy

Tajné klíče jsou `IConfigurationRoot.Reload()` ukládány do mezipaměti, dokud není volána. Vypršela platnost, zakázáno a aktualizované tajné klíče v trezoru klíčů nejsou respektovány aplikací, dokud `Reload` není proveden.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Zakázané a prošlé tajné klíče

Zakázané a vypršela <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>platnost tajných kódů hodit . Chcete-li zabránit vyvolání aplikace, zadejte konfiguraci pomocí jiného poskytovatele konfigurace nebo aktualizujte zakázaný tajný klíč nebo tajný klíč, jehož platnost vypršela.

## <a name="troubleshoot"></a>Řešení potíží

Když se aplikaci nepodaří načíst konfiguraci pomocí zprostředkovatele, zapíše se chybová zpráva do [infrastruktury ASP.NET core logging](xref:fundamentals/logging/index). Následující podmínky zabrání načtení konfigurace:

* Aplikace nebo certifikát není správně nakonfigurovaný ve službě Azure Active Directory.
* Trezor klíčů neexistuje v azure key vault.
* Aplikace nemá oprávnění k přístupu do trezoru klíčů.
* Zásady přístupu `Get` neobsahuje `List` a oprávnění.
* V trezoru klíčů jsou konfigurační data (dvojice název-hodnota) nesprávně pojmenována, chybí, zakázána nebo vypršela jejich platnost.
* Aplikace má nesprávný název`KeyVaultName`trezoru klíčů ( ),`AzureADApplicationId`ID aplikace Azure AD ( ) nebo kryptografický otisk certifikátu Azure AD (`AzureADCertThumbprint`).
* Konfigurační klíč (název) je v aplikaci nesprávný pro hodnotu, kterou se pokoušíte načíst.
* Při přidávání zásad přístupu pro aplikaci do trezoru klíčů byla zásada vytvořena, ale tlačítko **Uložit** nebylo v umělou iniciálním nastavení **zásad přístupu** vybráno.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Trezor klíčů](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Dokumentace trezoru klíčů](/azure/key-vault/)
* [Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Třída KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET](/azure/key-vault/quick-create-net)
* [Kurz: Jak používat Azure Key Vault s Azure Windows Virtual Machine v rozhraní .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

