---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ukládat a načítat citlivé informace jako tajné kódy aplikací během vývoje aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791589"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core na vývojovém počítači. Nikdy neuklápějte hesla nebo jiná citlivá data do zdrojového kódu. Produkční tajné kódy by neměly být používány pro vývoj nebo testování. Tajné klíče by neměly být nasazeny s aplikací. Místo toho tajné klíče by měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí se používají k zabránění ukládání tajných kódů aplikací v kódu nebo v místních konfiguračních souborech. Proměnné prostředí přepíší hodnoty konfigurace pro všechny dříve určené zdroje konfigurace.

Zvažte ASP.NET základní webovou aplikaci, ve které je povoleno zabezpečení **jednotlivých uživatelských účtů.** Výchozí připojovací řetězec databáze je součástí souboru *appsettings.json* projektu s klíčem `DefaultConnection`. Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo. Během nasazení aplikace `DefaultConnection` může být hodnota klíče přepsána hodnotou proměnné prostředí. Proměnná prostředí může uložit úplný připojovací řetězec s citlivými pověřeními.

> [!WARNING]
> Proměnné prostředí jsou obvykle uloženy v prostém, nešifrovaném textu. Pokud je počítač nebo proces ohrožen, proměnné prostředí mohou přistupovat nedůvěryhodné strany. Mohou být vyžadována další opatření, která zabrání zveřejnění tajných tajemství uživatelů.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Tajný správce

Nástroj Správce tajných kódů ukládá citlivá data během vývoje projektu ASP.NET Core. V tomto kontextu část citlivá data je tajný klíč aplikace. Tajné klíče aplikací jsou uloženy na jiném místě ze stromu projektu. Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny v několika projektech. Tajné klíče aplikace nejsou zaškrtnuté do správy zdrojového kódu.

> [!WARNING]
> Nástroj Správce tajných barev nešifruje uložené tajné klíče a neměl by být považován za důvěryhodný obchod. Je to jen pro účely vývoje. Klíče a hodnoty jsou uloženy v konfiguračním souboru JSON v adresáři profilu uživatele.

## <a name="how-the-secret-manager-tool-works"></a>Jak nástroj Správce tajných služeb funguje

Nástroj Správce tajných služeb abstrahuje podrobnosti implementace, například kde a jak jsou uloženy hodnoty. Nástroj můžete použít bez znalosti těchto podrobností implementace. Hodnoty jsou uloženy v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:

# <a name="windows"></a>[Windows](#tab/windows)

Cesta k systému souborů:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Cesta k systému souborů:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

V předchozích cestách souborů `<user_secrets_id>` nahraďte hodnotou `UserSecretsId` zadanou v souboru *.csproj.*

Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných barev. Tyto podrobnosti implementace se mohou změnit. Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.

## <a name="enable-secret-storage"></a>Povolení tajného úložiště

Nástroj Správce tajných barev pracuje s nastavením konfigurace specifickým pro projekt uloženým ve vašem uživatelském profilu.

Nástroj Správce tajných `init` barev obsahuje příkaz v sadě .NET Core SDK 3.0.100 nebo novější. Chcete-li použít tajné klíče uživatelů, spusťte v adresáři projektu následující příkaz:

```dotnetcli
dotnet user-secrets init
```

Předchozí příkaz přidá `UserSecretsId` prvek do `PropertyGroup` souboru *.csproj.* Ve výchozím nastavení `UserSecretsId` je vnitřní text identifikátoru GUID. Vnitřní text je libovolný, ale je jedinečný pro projekt.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

V sadě Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumníkovi řešení a v místní nabídce vyberte **Spravovat tajné kódy uživatelů.** Toto gesto `UserSecretsId` přidá prvek naplněný identifikátorem GUID do souboru *.csproj.*

## <a name="set-a-secret"></a>Nastavení tajného klíče

Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty. Tajný klíč je spojen s `UserSecretsId` hodnotou projektu. Spusťte například následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

V předchozím příkladu dvojtečka `Movies` označuje, že je `ServiceApiKey` literál objektu s vlastností.

Nástroj Správce tajných barev lze použít také z jiných adresářů. Pomocí `--project` možnosti můžete zadat cestu k systému souborů, ve které existuje soubor *CSProJ.* Příklad:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Sloučení struktury JSON v sadě Visual Studio

Gesto Spravovat **tajné klíče uživatelů** sady Visual Studio otevře soubor *secrets.json* v textovém editoru. Nahraďte obsah *souboru secrets.json* dvojicemi klíč-hodnota, které mají být uloženy. Příklad:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON je po úpravách `dotnet user-secrets remove` zploštělá pomocí nebo `dotnet user-secrets set`. Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál objektu. `Movies` Změněný soubor se podobá následujícímu:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Nastavení více tajných kódů

Dávku tajných kódů lze nastavit potrubím `set` JSON do příkazu. V následujícím příkladu jsou obsah souboru *input.json* `set` uveden do příkazu.

# <a name="windows"></a>[Windows](#tab/windows)

Otevřete příkazové prostředí a proveďte následující příkaz:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Otevřete příkazové prostředí a proveďte následující příkaz:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Přístup k tajnému klíči

Rozhraní [ASP.NET jádrové konfigurace rozhraní API](xref:fundamentals/configuration/index) poskytuje přístup k tajným klíčům správce tajných barev.

Zdroj konfigurace uživatelských tajných kódů je <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> automaticky přidán do režimu vývoje, když projekt volá k inicializaci nové instance hostitele s předkonfigurovanými výchozími hodnotami. `CreateDefaultBuilder`volání, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> když <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>je :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Když `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>uživatelských tajných kódů explicitně voláním . Volat `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Uživatelské tajné klíče lze `Configuration` načíst prostřednictvím rozhraní API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapovat tajné kódy na POCO

Mapování literálu celého objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci souvisejících vlastností.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Chcete-li mapovat předchozí tajné klíče `Configuration` na POCO, použijte funkci [vazby vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API. Následující kód se váže `MovieSettings` na vlastní POCO `ServiceApiKey` a přistupuje k hodnotě vlastnosti:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

A `Movies:ConnectionString` `Movies:ServiceApiKey` tajné klíče jsou mapovány `MovieSettings`na příslušné vlastnosti v :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Nahrazení řetězce tajnými kódy

Ukládání hesel ve formátu prostého textu je nezabezpečené. Například připojovací řetězec databáze uložený v *souboru appsettings.json* může obsahovat heslo pro zadaného uživatele:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpečnější přístup je uložení hesla jako tajný klíč. Příklad:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v *souboru appsettings.json*. Příklad:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Hodnotu tajného klíče lze <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nastavit na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu k dokončení připojovacího řetězce:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Seznam tajemství

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets list
```

Zobrazí se výstup:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *souboru secrets.json*.

## <a name="remove-a-single-secret"></a>Odebrání jednoho tajného klíče

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Soubor *secrets.json* aplikace byl upraven tak, aby odebral `MoviesConnectionString` dvojici klíč-hodnota přidruženou ke klíči:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`zobrazí následující zprávu:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Odebrání všech tajných kódů

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets clear
```

Ze souboru *secrets.json* byly odstraněny všechny tajné klíče uživatelů aplikace:

```json
{}
```

Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Další zdroje

* Informace o přístupu správce tajných barev ze služby IIS naleznete v [tomto problému.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core na vývojovém počítači. Nikdy neuklápějte hesla nebo jiná citlivá data do zdrojového kódu. Produkční tajné kódy by neměly být používány pro vývoj nebo testování. Tajné klíče by neměly být nasazeny s aplikací. Místo toho tajné klíče by měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Tajné klíče azure test a produkční verze můžete ukládat a chránit pomocí [zprostředkovatele konfigurace azure key vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí se používají k zabránění ukládání tajných kódů aplikací v kódu nebo v místních konfiguračních souborech. Proměnné prostředí přepíší hodnoty konfigurace pro všechny dříve určené zdroje konfigurace.

Zvažte ASP.NET základní webovou aplikaci, ve které je povoleno zabezpečení **jednotlivých uživatelských účtů.** Výchozí připojovací řetězec databáze je součástí souboru *appsettings.json* projektu s klíčem `DefaultConnection`. Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo. Během nasazení aplikace `DefaultConnection` může být hodnota klíče přepsána hodnotou proměnné prostředí. Proměnná prostředí může uložit úplný připojovací řetězec s citlivými pověřeními.

> [!WARNING]
> Proměnné prostředí jsou obvykle uloženy v prostém, nešifrovaném textu. Pokud je počítač nebo proces ohrožen, proměnné prostředí mohou přistupovat nedůvěryhodné strany. Mohou být vyžadována další opatření, která zabrání zveřejnění tajných tajemství uživatelů.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Tajný správce

Nástroj Správce tajných kódů ukládá citlivá data během vývoje projektu ASP.NET Core. V tomto kontextu část citlivá data je tajný klíč aplikace. Tajné klíče aplikací jsou uloženy na jiném místě ze stromu projektu. Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny v několika projektech. Tajné klíče aplikace nejsou zaškrtnuté do správy zdrojového kódu.

> [!WARNING]
> Nástroj Správce tajných barev nešifruje uložené tajné klíče a neměl by být považován za důvěryhodný obchod. Je to jen pro účely vývoje. Klíče a hodnoty jsou uloženy v konfiguračním souboru JSON v adresáři profilu uživatele.

## <a name="how-the-secret-manager-tool-works"></a>Jak nástroj Správce tajných služeb funguje

Nástroj Správce tajných služeb abstrahuje podrobnosti implementace, například kde a jak jsou uloženy hodnoty. Nástroj můžete použít bez znalosti těchto podrobností implementace. Hodnoty jsou uloženy v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:

# <a name="windows"></a>[Windows](#tab/windows)

Cesta k systému souborů:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Cesta k systému souborů:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

V předchozích cestách souborů `<user_secrets_id>` nahraďte hodnotou `UserSecretsId` zadanou v souboru *.csproj.*

Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných barev. Tyto podrobnosti implementace se mohou změnit. Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.

## <a name="enable-secret-storage"></a>Povolení tajného úložiště

Nástroj Správce tajných barev pracuje s nastavením konfigurace specifickým pro projekt uloženým ve vašem uživatelském profilu.

Chcete-li použít tajné `UserSecretsId` klíče `PropertyGroup` uživatelů, definujte prvek v rámci souboru *.csproj.* Vnitřní text `UserSecretsId` je libovolný, ale je jedinečný pro projekt. Vývojáři obvykle generují `UserSecretsId`identifikátor GUID pro rozhraní .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> V sadě Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumníkovi řešení a v místní nabídce vyberte **Spravovat tajné kódy uživatelů.** Toto gesto `UserSecretsId` přidá prvek naplněný identifikátorem GUID do souboru *.csproj.*

## <a name="set-a-secret"></a>Nastavení tajného klíče

Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty. Tajný klíč je spojen s `UserSecretsId` hodnotou projektu. Spusťte například následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

V předchozím příkladu dvojtečka `Movies` označuje, že je `ServiceApiKey` literál objektu s vlastností.

Nástroj Správce tajných barev lze použít také z jiných adresářů. Pomocí `--project` možnosti můžete zadat cestu k systému souborů, ve které existuje soubor *CSProJ.* Příklad:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Sloučení struktury JSON v sadě Visual Studio

Gesto Spravovat **tajné klíče uživatelů** sady Visual Studio otevře soubor *secrets.json* v textovém editoru. Nahraďte obsah *souboru secrets.json* dvojicemi klíč-hodnota, které mají být uloženy. Příklad:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON je po úpravách `dotnet user-secrets remove` zploštělá pomocí nebo `dotnet user-secrets set`. Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí literál objektu. `Movies` Změněný soubor se podobá následujícímu:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Nastavení více tajných kódů

Dávku tajných kódů lze nastavit potrubím `set` JSON do příkazu. V následujícím příkladu jsou obsah souboru *input.json* `set` uveden do příkazu.

# <a name="windows"></a>[Windows](#tab/windows)

Otevřete příkazové prostředí a proveďte následující příkaz:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Otevřete příkazové prostředí a proveďte následující příkaz:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Přístup k tajnému klíči

Rozhraní [ASP.NET jádrové konfigurace rozhraní API](xref:fundamentals/configuration/index) poskytuje přístup k tajným klíčům správce tajných barev.

Pokud váš projekt cílí na rozhraní .NET Framework, nainstalujte balíček [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.

V ASP.NET Core 2.0 nebo novější, zdroj konfigurace uživatelských tajných <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> kódů je automaticky přidán do režimu vývoje, když projekt volá inicializovat novou instanci hostitele s předkonfigurovanými výchozími hodnotami. `CreateDefaultBuilder`volání, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> když <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>je :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Když `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> uživatelských `Startup` tajných kódů explicitně voláním v konstruktoru. Volat `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Uživatelské tajné klíče lze `Configuration` načíst prostřednictvím rozhraní API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapovat tajné kódy na POCO

Mapování literálu celého objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci souvisejících vlastností.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Chcete-li mapovat předchozí tajné klíče `Configuration` na POCO, použijte funkci [vazby vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API. Následující kód se váže `MovieSettings` na vlastní POCO `ServiceApiKey` a přistupuje k hodnotě vlastnosti:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

A `Movies:ConnectionString` `Movies:ServiceApiKey` tajné klíče jsou mapovány `MovieSettings`na příslušné vlastnosti v :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Nahrazení řetězce tajnými kódy

Ukládání hesel ve formátu prostého textu je nezabezpečené. Například připojovací řetězec databáze uložený v *souboru appsettings.json* může obsahovat heslo pro zadaného uživatele:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpečnější přístup je uložení hesla jako tajný klíč. Příklad:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Odeberte dvojici `Password` klíč-hodnota z připojovacího řetězce v *souboru appsettings.json*. Příklad:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Hodnotu tajného klíče lze <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nastavit na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu k dokončení připojovacího řetězce:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Seznam tajemství

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets list
```

Zobrazí se výstup:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *souboru secrets.json*.

## <a name="remove-a-single-secret"></a>Odebrání jednoho tajného klíče

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Soubor *secrets.json* aplikace byl upraven tak, aby odebral `MoviesConnectionString` dvojici klíč-hodnota přidruženou ke klíči:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Odebrání všech tajných kódů

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém existuje soubor *CSProJ:*

```dotnetcli
dotnet user-secrets clear
```

Ze souboru *secrets.json* byly odstraněny všechny tajné klíče uživatelů aplikace:

```json
{}
```

Spuštěno `dotnet user-secrets list` zobrazí následující zprávu:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Další zdroje

* Informace o přístupu správce tajných barev ze služby IIS naleznete v [tomto problému.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end