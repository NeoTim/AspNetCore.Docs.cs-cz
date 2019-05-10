---
title: Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat aplikaci pomocí dvojice název hodnota v době běhu načteny pomocí zprostředkovatele konfigurace trezoru klíčů Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 45eca05b5eb41815924ca48f60c3b00046c6bdaf
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901038"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Poskytovatel konfigurace služby Azure Key Vault v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Andrew Stanton sestry](https://github.com/anurse)

Tento dokument popisuje, jak používat [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) poskytovatel konfigurace pro načtení hodnoty konfigurace aplikace z Azure Key Vault tajných kódů. Azure Key Vault je Cloudová služba, která pomáhá zajistit ochranu kryptografických klíčů a tajných kódů používaných aplikacemi a službami. Časté scénáře pro používání služby Azure Key Vault s aplikací ASP.NET Core patří:

* Řízení přístupu k citlivým konfigurační data.
* Splnění požadavků pro FIPS 140-2 úrovně 2 ověřit modulů hardwarového zabezpečení (HSM) při ukládání konfigurační data.

Tento scénář je k dispozici pro aplikace, které cílí ASP.NET Core 2.1 nebo novější.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Balíčky

Použití zprostředkovatele konfigurace trezoru klíčů Azure, přidejte odkaz na balíček [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) balíčku.

Přijmout [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) scénář, přidejte odkaz na balíček [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) balíčku.

> [!NOTE]
> V době psaní, nejnovější stabilní verzi `Microsoft.Azure.Services.AppAuthentication`, verze `1.0.3`, poskytuje podporu pro [systém přiřadil spravovaných identit](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka). Podpora pro *uživatelsky přiřazené identity spravované* je k dispozici v `1.2.0-preview2` balíčku. Toto téma popisuje použití identit spravovaných systému a připravená ukázková aplikace používá verzi `1.0.3` z `Microsoft.Azure.Services.AppAuthentication` balíčku.

## <a name="sample-app"></a>Ukázková aplikace

Ukázková aplikace spouští v jednom ze dvou režimů, které jsou určeny `#define` příkazu v horní části *Program.cs* souboru:

* `Certificate` &ndash; Ukazuje použití certifikát ID klienta aplikace Azure Key Vault a X.509 pro přístup k tajnými kódy uloženými v Azure Key Vault. Tuto verzi vzorku můžete spustit z libovolného místa a nasadit do Azure App Service nebo libovolného hostitele, která je schopná obsluhovat aplikace ASP.NET Core.
* `Managed` &ndash; Ukazuje, jak používat [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) k ověření aplikace do služby Azure Key Vault pomocí ověřování Azure AD bez přihlašovací údaje uložené v kódu nebo konfigurace aplikace. Při použití spravované identity k ověření, ID aplikace Azure AD a heslo (tajný klíč klienta) se nevyžadují. `Managed` Verzi vzorku se musí nasadit do Azure. Postupujte podle pokynů v [pomocí spravované identity pro prostředky Azure](#use-managed-identities-for-azure-resources) oddílu.

Další informace o tom, jak nakonfigurovat ukázkovou aplikaci pomocí direktivy preprocesoru (`#define`), najdete v článku <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Úložiště tajných kódů ve vývojovém prostředí

Nastavení tajných kódů pomocí místně [nástroj tajný klíč správce](xref:security/app-secrets). Spuštění ukázkové aplikace v místním počítači, ve vývojovém prostředí jsou načteny tajné klíče z místního úložiště tajný klíč správce.

Vyžaduje nástroj tajný klíč správce `<UserSecretsId>` vlastnost v souboru projektu vaší aplikace. Nastavte hodnotu vlastnosti (`{GUID}`) na libovolný jedinečný identifikátor GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Tajné kódy jsou vytvořeny jako dvojice název hodnota. Použít hierarchická hodnoty (konfigurační oddíly funkce) `:` (dvojtečka) jako oddělovač v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index) názvů klíčů.

Tajný klíč správce se používá z příkazové okno otevřít obsahu kořen projektu, kde `{SECRET NAME}` je název a `{SECRET VALUE}` je hodnota:

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Spusťte následující příkazy v příkazovém řádku z obsahu kořenového adresáře projektu k nastavení tajných kódů pro ukázkovou aplikaci:

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Když tyto tajné kódy jsou uložené ve službě Azure Key Vault v [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) části `_dev` přípona se změní na `_prod`. Přípona poskytuje vizuální upozornění ve výstupu aplikace označující zdrojové hodnoty konfigurace.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault

Podle pokynů uvedených [rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI](/azure/key-vault/quick-create-cli) tématu této části jsou shrnuty pro vytvoření služby Azure Key Vault a uložení tajných kódů používaných ukázkovou aplikaci. Viz téma pro další podrobnosti.

1. Otevřete Azure Cloud shell pomocí některého z následujících metod v [webu Azure portal](https://portal.azure.com/):

   * Vyberte **vyzkoušet** v pravém horním rohu bloku kódu. Použijte hledaný řetězec "Azure CLI" v textovém poli.
   * Otevřete Cloud Shell v prohlížeči s **spustit Cloud Shell** tlačítko.
   * Vyberte **Cloud Shell** tlačítko v pravém horním rohu webu Azure portal v nabídce.

   Další informace najdete v tématu [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) a [Přehled služby Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pokud již nejste ověřeni, přihlaste se `az login` příkazu.

1. Vytvořte skupinu prostředků pomocí následujícího příkazu, kde `{RESOURCE GROUP NAME}` je název skupiny prostředků pro nové skupiny prostředků a `{LOCATION}` je oblast Azure (datacenter):

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvoření trezoru klíčů ve skupině prostředků pomocí následujícího příkazu, kde `{KEY VAULT NAME}` je název pro nový trezor klíčů a `{LOCATION}` je oblast Azure (datacenter):

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Vytvoření tajných kódů v trezoru klíčů jako dvojice název hodnota.

   Azure Key Vault tajný názvy jsou omezené na alfanumerické znaky a pomlčky. Hierarchické hodnoty (konfigurační oddíly) použijte `--` (dvě pomlčky) jako oddělovač. Použití dvojteček, které se obvykle používají pro vymezení část z podklíč v [konfigurace ASP.NET Core](xref:fundamentals/configuration/index), nejsou povoleny v názvech tajného kódu trezoru klíčů. Proto jsou použít dvě pomlčky a má Prohodit pro dvojtečkou tajné klíče jsou načtena do konfigurace aplikace.

   Tyto tajné klíče jsou pro použití s ukázkovou aplikací. Hodnoty patří `_prod` přípona k odlišení od `_dev` přípony hodnoty načteny ve vývojovém prostředí od tajných kódů uživatelů. Nahraďte `{KEY VAULT NAME}` s názvem služby key vault, kterou jste vytvořili v předchozím kroku:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a>Pomocí ID aplikace a tajný kód klienta pro jiné Azure hostované aplikace

Konfigurace služby Azure AD, Azure Key Vault a aplikace použít Azure Active Directory ID aplikace a X.509 certifikátu ověřování do služby key vault **když je aplikace hostovaná mimo Azure**. Další informace najdete v tématu [informace o klíčích, tajných kódů a certifikátů](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> I když se pomocí ID aplikace a X.509 certifikátu se nepodporují pro aplikace hostované v Azure, doporučujeme používat [spravovaných identit pro prostředky Azure](#use-managed-identities-for-azure-resources) při hostování aplikace v Azure. Spravované identity nevyžadují ukládání certifikátu v aplikaci nebo ve vývojovém prostředí.

Tato ukázková aplikace používá ID aplikace a služby X.509 certifikátu při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Certificate`.

1. Registrace aplikace v Azure AD (**registrace aplikací**).
1. Odešlete veřejný klíč:
   1. Vyberte aplikaci ve službě Azure AD.
   1. Přejděte do **nastavení** > **klíče**.
   1. Vyberte **nahrát veřejný klíč** se nahrát certifikát, který obsahuje veřejný klíč. Kromě použití *.cer*, *.pem*, nebo *.crt* certifikát, *.pfx* certifikát jde nahrát.
1. Store název trezoru klíčů a ID aplikace v aplikaci prvku *appsettings.json* souboru. Certifikát umístěte do kořenové aplikace nebo aplikace do úložiště certifikátů&dagger;.
1. Přejděte do **trezory klíčů** na webu Azure Portal.
1. Vyberte trezor klíčů, které jste vytvořili [úložiště tajných kódů v produkčním prostředí pomocí služby Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) oddílu.
1. Vyberte **zásady přístupu**.
1. Vyberte **přidat nový**.
1. Vyberte **vybrat objekt zabezpečení** a vybrat registrovaná aplikace podle názvu. Vyberte **vyberte** tlačítko.
1. Otevřít **oprávnění tajného klíče** a zajišťují aplikaci s **získat** a **seznamu** oprávnění.
1. Vyberte **OK**.
1. Vyberte **Uložit**.
1. Nasazení aplikace.

&dagger;V ukázkové aplikaci, certifikát se spotřebovává přímo z fyzického souboru v kořenovém adresáři aplikace tak, že vytvoříte nový `X509Certificate2` při volání metody `AddAzureKeyVault`. Alternativním přístupem je umožnit operačního systému pro správu certifikát. Další informace najdete v tématu [povolit operačního systému pro správu certifikát X.509](#allow-the-os-to-manage-the-x509-certificate) oddílu.

`Certificate` Ukázkové aplikace získá jeho hodnoty konfigurace z `IConfigurationRoot` se stejným názvem jako název tajného kódu:

* Hierarchické bez hodnoty: Hodnota pro `SecretName` se získá pomocí `config["SecretName"]`.
* Hierarchické hodnoty (oddílů): Použití `:` zápis (dvojtečka) nebo `GetSection` – metoda rozšíření. Použijte některou z těchto přístupů k získání hodnoty konfigurace:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Volání aplikace `AddAzureKeyVault` hodnotami poskytnutých *appsettings.json* souboru:

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=12-15)]

Ukázkové hodnoty:

* Název trezoru klíčů: `contosovault`
* ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů. Ve vývojovém prostředí načíst hodnoty tajných kódů se `_dev` příponu. V produkčním prostředí a hodnoty načíst data pomocí `_prod` příponu.

## <a name="use-managed-identities-for-azure-resources"></a>Použití spravované identity pro prostředky Azure

**Aplikace nasazené do Azure** můžou těžit z výhod [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview), který umožňuje aplikaci k ověřování pomocí Azure Key Vault pomocí ověřování Azure AD bez pověření (ID aplikace a Tajný kód Password/Client) uložených v aplikaci.

Ukázková aplikace používá identity spravované pro prostředky Azure při `#define` příkazu v horní části *Program.cs* souboru má nastavenou `Managed`.

Zadejte název trezoru do aplikace *appsettings.json* souboru. Ukázková aplikace nevyžaduje, aby aplikace ID a heslo (tajný klíč klienta), pokud je nastavena na `Managed` verze, takže tyto položky konfigurace, můžete ignorovat. Nasazení aplikace do Azure a Azure ověřuje aplikaci přístup k použití pouze na název trezoru uložených v Azure Key Vault *appsettings.json* souboru.

Nasazení ukázkové aplikace do služby Azure App Service.

Aplikace nasazené do služby Azure App Service při vytvoření služby Azure AD automaticky zaregistruje. Získání ID objektu z nasazení pro použití v následujícím příkazu. ID objektu se zobrazí na webu Azure Portal na **Identity** panel služby App Service.

Pomocí Azure CLI a ID objektu aplikace, zadejte tyto aplikace s `list` a `get` oprávnění pro přístup k trezoru klíčů:

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**Restartujte aplikaci** pomocí rozhraní příkazového řádku Azure, Powershellu nebo na webu Azure portal.

Ukázkové aplikace:

* Vytvoří instanci `AzureServiceTokenProvider` třídy bez připojovací řetězec. Když řetězec připojení není k dispozici, zprostředkovatel pokusy o získání přístupového tokenu ze spravovaných identit pro prostředky Azure.
* Nový `KeyVaultClient` se vytvoří s `AzureServiceTokenProvider` instance tokenu zpětného volání.
* `KeyVaultClient` Instance se používá s výchozí implementaci třídy `IKeyVaultSecretManager` , který načte všechny hodnoty tajných kódů a nahradí double pomlčky (`--`) pomocí dvojtečky (`:`) v názvech klíčů.

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

Při spuštění aplikace, webová stránka zobrazuje načíst hodnoty tajných kódů. Ve vývojovém prostředí mají hodnoty tajných kódů `_dev` příponu, protože jste poskytované tajných kódů uživatelů. V produkčním prostředí a hodnoty načíst data pomocí `_prod` přípony vzhledem k tomu, že poskytovaný Azure Key Vault.

Pokud se zobrazí `Access denied` chyba, zkontrolujte, jestli je aplikace v Azure AD zaregistrováno a poskytuje přístup k trezoru klíčů. Potvrďte, že jste restartovat službu v Azure.

## <a name="use-a-key-name-prefix"></a>Použijte předponu názvu klíče

`AddAzureKeyVault` poskytuje přetížení přijímající implementace `IKeyVaultSecretManager`, což vám umožňuje řídit jak klíče trezoru tajných kódů se převedou na konfigurační klíče. Například můžete implementovat rozhraní za účelem načtení hodnoty tajných kódů na základě hodnoty předpony, které poskytnete při spuštění aplikace. Díky tomu můžete například načíst tajné kódy na základě verze aplikace.

> [!WARNING]
> Nepoužívejte předpony v tajných kódů služby key vault, umístí tajných kódů pro více aplikací do stejného trezoru klíčů nebo umístit prostředí tajné kódy (například *vývoj* oproti *produkční* tajné kódy) do jedné trezor. Doporučujeme různých aplikací a vývojové nebo produkční prostředí použít samostatné trezorům klíčů k izolování aplikace prostředí pro nejvyšší úroveň zabezpečení.

V následujícím příkladu se v klíči naváže tajného kódu trezoru (a pomocí nástroje Správce tajný klíč pro vývojové prostředí) pro `5000-AppSecret` (tečky nejsou povoleny v názvech tajných kódů služby key vault). Tento tajný kód představuje tajný kód aplikace pro 5.0.0.0 verzi aplikace. Pro jinou verzi aplikace, 5.1.0.0, tajného kódu se přidá do klíče trezoru (a pomocí nástroje Správce tajný klíč) pro `5100-AppSecret`. Jednotlivé verze aplikace načte do jeho konfigurace jako jeho verzované tajná hodnota `AppSecret`, vypuzovacího vypnout verze během načítání tajného klíče.

`AddAzureKeyVault` je volána s vlastní `IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

Poskytuje hodnoty pro název trezoru klíčů, ID aplikace a heslo (tajný klíč klienta) *appsettings.json* souboru:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Ukázkové hodnoty:

* Název trezoru klíčů: `contosovault`
* ID aplikace: `627e911e-43cc-61d4-992e-12db9c81b413`
* Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

`IKeyVaultSecretManager` Implementace jsou reaguje na verze předpony tajných kódů k načtení správné tajného klíče do konfigurace:

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

`Load` Metoda je volána metodou poskytovatele algoritmus, který Iteruje přes našli ty, které mají předponu verze tajné klíče trezoru. Když se nachází verze předpony s `Load`, používá algoritmus `GetKey` metoda vrátí název konfigurace název tajného kódu. Odstraní vypnout verze předpony z názvu tajného klíče a vrátí zbytek název tajného kódu pro načtení do konfigurace aplikace dvojice název hodnota.

Když je tento přístup implementovat:

1. Verze aplikace zadaná v souboru projektu vaší aplikace. V následujícím příkladu, verze aplikace nastavená na `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Ujistěte se, že `<UserSecretsId>` vlastnost je k dispozici v souboru projektu vaší aplikace, kde `{GUID}` je uživatelský identifikátor GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Uložte tyto tajné kódy místně [nástroj tajný klíč správce](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Tajné klíče jsou uložené ve službě Azure Key Vault pomocí následujících příkazů Azure CLI:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Při spuštění aplikace se načítají tajných kódů služby key vault. Řetězec tajný klíč pro `5000-AppSecret` odpovídá verzi aplikace zadanou v souboru projektu vaší aplikace (`5.0.0.0`).

1. Verze, `5000` (s pomlčkou), je odebrána od názvu klíče. V celé aplikaci, čte se konfigurace s klíčem `AppSecret` načte hodnota tajného klíče.

1. Pokud verze aplikace se změnilo v souboru projektu se `5.1.0.0` a aplikace znovu spustí, je vrácena hodnota tajného klíče `5.1.0.0_secret_value_dev` ve vývojovém prostředí a `5.1.0.0_secret_value_prod` v produkčním prostředí.

> [!NOTE]
> Můžete taky zadat vlastní `KeyVaultClient` implementaci `AddAzureKeyVault`. Vlastního klienta umožňuje sdílení jedné instance klienta aplikace.

## <a name="allow-the-os-to-manage-the-x509-certificate"></a>Povolit operačního systému pro správu certifikátů X.509

Certifikát X.509 je možné spravovat podle operačního systému. V následujícím příkladu `AddAzureKeyVault` přetížení přijímající `X509Certificate2` z úložiště certifikátů aktuálního uživatele počítači a kryptografický otisk certifikátu zadaný v konfiguraci:

```csharp
// using System.Linq;
// using System.Security.Cryptography.X509Certificates;
// using Microsoft.Extensions.Configuration;

WebHost.CreateDefaultBuilder(args)
    .ConfigureAppConfiguration((context, config) =>
    {
        if (context.HostingEnvironment.IsProduction())
        {
            var builtConfig = config.Build();

            using (var store = new X509Store(StoreName.My, 
                StoreLocation.CurrentUser))
            {
                store.Open(OpenFlags.ReadOnly);
                var certs = store.Certificates
                    .Find(X509FindType.FindByThumbprint, 
                        builtConfig["CertificateThumbprint"], false);

                config.AddAzureKeyVault(
                    builtConfig["KeyVaultName"], 
                    builtConfig["AzureADApplicationId"], 
                    certs.OfType<X509Certificate2>().Single());

                store.Close();
            }
        }
    })
    .UseStartup<Startup>();
```

Další informace najdete v tématu [ověřování pomocí certifikátu místo tajného klíče klienta](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).

## <a name="bind-an-array-to-a-class"></a>Svázat pole třídy

Zprostředkovatel je schopný načíst konfigurační hodnoty do pole pro vazbu k poli POCO.

Při čtení ze zdroje konfigurace, která umožňuje klíče obsahovat dvojtečku (`:`) oddělovače, číselné klíčové segment, který se používá k rozlišení klíčů, které tvoří pole (`:0:`, `:1:`;... `:{n}:`). Další informace najdete v tématu [konfigurace: Svázat pole třídy](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault klíče nelze použít dvojtečku jako oddělovač. Postupu popsaného v tomto tématu používá dvojité pomlčky (`--`) jako oddělovač pro hierarchické hodnoty (oddíly). Pole klíče jsou uložené ve službě Azure Key Vault s double pomlčky a číselné klíčových segmentů (`--0--`, `--1--`, &hellip; `--{n}--`).

Zkontrolujte následující [Serilog](https://serilog.net/) protokolování konfigurace poskytovatele poskytovaný souborem JSON. Existují dva literály definované v objektu `WriteTo` pole, které zahrnují dva Serilog *jímky*, které popisují cíle pro výstup protokolování:

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

Konfigurace je znázorněno v předchozím soubor JSON je uložená ve službě Azure Key Vault pomocí dvojitá čárka (`--`) zápisem a číselné segmenty:

| Key | Hodnota |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Znovu načíst tajné kódy

Tajné klíče jsou uložené v mezipaměti až do `IConfigurationRoot.Reload()` je volána. Vypršela platnost, zakázané, a aktualizované tajné kódy ve službě key vault není respektována aplikace do `Reload` provádí.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Tajné kódy zakázaná a jejichž platnost vypršela

Vyvolat zakázaná a jejichž platnost vypršela tajných kódů `KeyVaultClientException`. Abyste zabránili vyvolání vaší aplikace, nahradit aplikaci nebo aktualizovat tajný kód zakázaný nebo vypršela platnost.

## <a name="troubleshoot"></a>Řešení potíží

Když aplikaci se pak nepodaří načíst konfiguraci pomocí zprostředkovatele, chybová zpráva je zapsána do [ASP.NET Core protokolování infrastruktury](xref:fundamentals/logging/index). Konfigurace načítání nebudou moct tyto podmínky:

* Aplikace není správně nakonfigurovaný v Azure Active Directory.
* Trezor klíčů neexistuje ve službě Azure Key Vault.
* Aplikace nemá oprávnění pro přístup k trezoru klíčů.
* Zásady přístupu neobsahuje `Get` a `List` oprávnění.
* Ve službě key vault konfigurační data (dvojice název hodnota) je nesprávně pojmenované, chybí, zakázán, nebo vypršela platnost.
* Aplikace má název chybný trezoru klíčů (`KeyVaultName`), Id aplikace Azure AD (`AzureADApplicationId`), nebo hesel služby Azure AD (tajný klíč klienta) (`AzureADPassword`).
* Hesel služby Azure AD (tajný klíč klienta) (`AzureADPassword`) vypršela.
* Konfigurační klíč (název) je nesprávný v aplikaci pro hodnotu, kterou se pokoušíte načíst.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Trezor klíčů](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Dokumentace ke službě Key Vault](/azure/key-vault/)
* [Postup generování a přenos chráněných pomocí HSM klíčů pro Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Třída KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET](/azure/key-vault/quick-create-net)
* [Kurz: Jak používat Azure Key Vault s Windows virtuální počítač Azure v .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)
