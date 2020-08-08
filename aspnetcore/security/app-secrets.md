---
title: Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core
author: rick-anderson
description: Naučte se ukládat a načítat citlivé informace jako tajné klíče aplikace při vývoji aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 917e698d34a5d4b6c2c3f4737c08f1a590f5df1a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017945"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači. Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu. Provozní tajemství by se neměla používat pro vývoj nebo testování. Tajné klíče by se neměly nasazovat spolu s aplikací. Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech. Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.

Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** . Výchozí připojovací řetězec databáze je součástí *appsettings.js* projektu se souborem s klíčem `DefaultConnection` . Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo. Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí. Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.

> [!WARNING]
> Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu. Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami. Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Správce tajných klíčů

Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu. V tomto kontextu je část citlivých dat tajný klíč aplikace. Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu. Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty. Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.

> [!WARNING]
> Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště. Pouze pro účely vývoje. Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.

## <a name="how-the-secret-manager-tool-works"></a>Jak funguje nástroj Správce tajných klíčů

Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot. Tento nástroj můžete použít bez znalosti těchto podrobností implementace. Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:

# <a name="windows"></a>[Windows](#tab/windows)

Cesta k systému souborů:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Cesta k systému souborů:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .

Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů. Tyto podrobnosti implementace se můžou změnit. Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.

## <a name="enable-secret-storage"></a>Povolit tajné úložiště

Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.

Nástroj Správce tajných klíčů obsahuje `init` příkaz v .NET Core SDK 3.0.100 nebo novějším. Chcete-li použít tajné klíče uživatele, spusťte následující příkaz v adresáři projektu:

```dotnetcli
dotnet user-secrets init
```

Předchozí příkaz přidá `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* . Ve výchozím nastavení je vnitřní text pro `UserSecretsId` identifikátor GUID. Vnitřní text je libovolný, ale je pro projekt jedinečný.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** . Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .

## <a name="set-a-secret"></a>Nastavení tajného klíče

Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty. Tajný kód je přidružený k `UserSecretsId` hodnotě projektu. Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.

Nástroj Správce tajných klíčů je možné použít i v jiných adresářích. Pomocí `--project` Možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* . Například:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Sloučení struktury JSON v aplikaci Visual Studio

Gesto **správy uživatelských tajných** kódů v aplikaci Visual Studio otevře *secrets.js* v souboru v textovém editoru. Nahraďte obsah *secrets.js* s páry klíč-hodnota, které se mají uložit. Například:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo. shrnuta `dotnet user-secrets set` . Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literál objektu. Změněný soubor se podobá následujícímu:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Nastavení více tajných klíčů

Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz. V následujícím příkladu je *input.js* obsahu souboru kanálem do `set` příkazu.

# <a name="windows"></a>[Windows](#tab/windows)

Otevřete příkazové prostředí a spusťte následující příkaz:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Otevřete příkazové prostředí a spusťte následující příkaz:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Přístup ke tajnému kódu

[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.

Zdroj konfigurace uživatelských tajných klíčů je automaticky přidán do režimu vývoje, pokud projekt volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami. `CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, když <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> je <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> . Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapování tajných kódů na POCO

Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

K namapování předchozích tajných kódů na POCO použijte `Configuration` funkci [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API. Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k `ServiceApiKey` hodnotě vlastnosti:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Tajné kódy a `Movies:ServiceApiKey` jsou mapovány na příslušné vlastnosti v `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Výměna řetězců s tajnými kódy

Ukládání hesel v prostém textu je nezabezpečené. Připojovací řetězec databáze uložený v *appsettings.js* může například zahrnovat heslo pro zadaného uživatele:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpečnější je ukládání hesla jako tajného klíče. Například:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Odeberte `Password` dvojici klíč-hodnota z připojovacího řetězce v *appsettings.js*. Například:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Výpis tajných kódů

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets list
```

Objeví se následující výstup:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *secrets.js*.

## <a name="remove-a-single-secret"></a>Odebrání jednoho tajného klíče

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

*secrets.jsaplikace v* souboru se změnila tak, aby se odebral pár klíč-hodnota přidružený ke `MoviesConnectionString` klíči:

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

## <a name="remove-all-secrets"></a>Odebrat všechna tajná klíčová

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets clear
```

Všechny tajné klíče uživatele pro aplikaci byly z *secrets.jsv* souboru odstraněny:

```json
{}
```

`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Další zdroje

* Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27)a [Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Tento dokument vysvětluje techniky pro ukládání a načítání citlivých dat během vývoje ASP.NET Core aplikace na vývojovém počítači. Nikdy neukládejte hesla nebo další citlivá data ve zdrojovém kódu. Provozní tajemství by se neměla používat pro vývoj nebo testování. Tajné klíče by se neměly nasazovat spolu s aplikací. Místo toho by tajné klíče měly být k dispozici v produkčním prostředí prostřednictvím řízených prostředků, jako jsou proměnné prostředí, Azure Key Vault atd. Pomocí [poskytovatele konfigurace Azure Key Vault](xref:security/key-vault-configuration)můžete ukládat a chránit tajné kódy Azure test a produkčního prostředí.

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí se používají k tomu, aby nedocházelo k ukládání tajných klíčů k aplikacím v kódu nebo v místních konfiguračních souborech. Proměnné prostředí přepíšou konfigurační hodnoty pro všechny dříve zadané zdroje konfigurace.

Vezměte v úvahu ASP.NET Core webovou aplikaci, ve které je povolené zabezpečení **individuálních uživatelských účtů** . Výchozí připojovací řetězec databáze je součástí *appsettings.js* projektu se souborem s klíčem `DefaultConnection` . Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje heslo. Během nasazování aplikace se `DefaultConnection` hodnota klíče dá přepsat hodnotou proměnné prostředí. Proměnná prostředí může ukládat úplný připojovací řetězec s citlivými přihlašovacími údaji.

> [!WARNING]
> Proměnné prostředí se většinou ukládají v prostém, nešifrovaném textu. Pokud dojde k ohrožení bezpečnosti počítače nebo procesu, jsou proměnné prostředí dostupné nedůvěryhodnými stranami. Další opatření, která zabraňují odhalení tajných kódů uživateli, mohou být povinná.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Správce tajných klíčů

Nástroj Správce tajných klíčů ukládá citlivé údaje během vývoje ASP.NET Core projektu. V tomto kontextu je část citlivých dat tajný klíč aplikace. Tajné klíče aplikace se ukládají v odděleném umístění ze stromu projektu. Tajné klíče aplikace jsou přidruženy k určitému projektu nebo sdíleny napříč několika projekty. Tajné kódy aplikace nejsou zaregistrovány do správy zdrojového kódu.

> [!WARNING]
> Nástroj Správce tajných klíčů nešifruje uložené tajné klíče a neměl by být považován za důvěryhodné úložiště. Pouze pro účely vývoje. Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.

## <a name="how-the-secret-manager-tool-works"></a>Jak funguje nástroj Správce tajných klíčů

Nástroj Správce tajných klíčů vyříznout podrobnosti implementace, například WHERE a způsob uložení hodnot. Tento nástroj můžete použít bez znalosti těchto podrobností implementace. Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu uživatele chráněné systémem v místním počítači:

# <a name="windows"></a>[Windows](#tab/windows)

Cesta k systému souborů:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Cesta k systému souborů:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

V předchozích cestách souborů nahraďte `<user_secrets_id>` `UserSecretsId` hodnotou zadanou v souboru *. csproj* .

Nepište kód, který závisí na umístění nebo formátu dat uložených pomocí nástroje Správce tajných klíčů. Tyto podrobnosti implementace se můžou změnit. Například tajné hodnoty nejsou šifrovány, ale mohou být v budoucnu.

## <a name="enable-secret-storage"></a>Povolit tajné úložiště

Nástroj Správce tajných klíčů pracuje na nastavení konfigurace specifické pro projekt uložené v profilu uživatele.

Pro použití uživatelských tajných klíčů definujte `UserSecretsId` prvek v rámci `PropertyGroup` souboru *. csproj* . Vnitřní text `UserSecretsId` je libovolný, ale je pro projekt jedinečný. Vývojáři obvykle generují identifikátor GUID pro `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> V aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt v Průzkumník řešení a v místní nabídce vyberte možnost **spravovat tajné klíče uživatele** . Tento gesto přidá `UserSecretsId` prvek, který je vyplněn identifikátorem GUID, do souboru *. csproj* .

## <a name="set-a-secret"></a>Nastavení tajného klíče

Definujte tajný klíč aplikace skládající se z klíče a jeho hodnoty. Tajný kód je přidružený k `UserSecretsId` hodnotě projektu. Například spusťte následující příkaz z adresáře, ve kterém existuje soubor *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

V předchozím příkladu dvojtečka označuje `Movies` literál objektu s `ServiceApiKey` vlastností.

Nástroj Správce tajných klíčů je možné použít i v jiných adresářích. Pomocí `--project` Možnosti zadejte cestu k systému souborů, na které existuje soubor *. csproj* . Například:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Sloučení struktury JSON v aplikaci Visual Studio

Gesto **správy uživatelských tajných** kódů v aplikaci Visual Studio otevře *secrets.js* v souboru v textovém editoru. Nahraďte obsah *secrets.js* s páry klíč-hodnota, které se mají uložit. Například:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON je po úpravách prostřednictvím `dotnet user-secrets remove` nebo. shrnuta `dotnet user-secrets set` . Například spuštění `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literál objektu. Změněný soubor se podobá následujícímu:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Nastavení více tajných klíčů

Dávku tajných kódů lze nastavit pomocí `set` příkazuového řádku JSON pro příkaz. V následujícím příkladu je *input.js* obsahu souboru kanálem do `set` příkazu.

# <a name="windows"></a>[Windows](#tab/windows)

Otevřete příkazové prostředí a spusťte následující příkaz:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Otevřete příkazové prostředí a spusťte následující příkaz:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Přístup ke tajnému kódu

[Rozhraní API pro konfiguraci ASP.NET Core](xref:fundamentals/configuration/index) poskytuje přístup ke tajným tajným klíčům správce.

Pokud je projekt cílen .NET Framework, nainstalujte [Microsoft.Extensions.Configuration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)Balíček NuGet UserSecrets

V ASP.NET Core 2,0 nebo novějším je zdroj konfigurace tajných kódů uživatelských tajných kódů automaticky přidán do režimu vývoje, pokud projekt volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> inicializaci nové instance hostitele s předem konfigurovanými výchozími hodnotami. `CreateDefaultBuilder`volá <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> se, když <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> je <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Pokud `CreateDefaultBuilder` není volána, přidejte zdroj konfigurace tajných klíčů uživatele explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> v `Startup` konstruktoru. Volejte `AddUserSecrets` pouze v případě, že aplikace běží ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Uživatelské tajné kódy lze načíst prostřednictvím `Configuration` rozhraní API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapování tajných kódů na POCO

Mapování celého literálu objektu na POCO (jednoduchá třída .NET s vlastnostmi) je užitečná pro agregaci souvisejících vlastností.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

K namapování předchozích tajných kódů na POCO použijte `Configuration` funkci [vazby grafu objektů](xref:fundamentals/configuration/index#bind-to-an-object-graph) rozhraní API. Následující kód se váže k vlastnímu `MovieSettings` POCO a přistupuje k `ServiceApiKey` hodnotě vlastnosti:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Tajné kódy a `Movies:ServiceApiKey` jsou mapovány na příslušné vlastnosti v `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Výměna řetězců s tajnými kódy

Ukládání hesel v prostém textu je nezabezpečené. Připojovací řetězec databáze uložený v *appsettings.js* může například zahrnovat heslo pro zadaného uživatele:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpečnější je ukládání hesla jako tajného klíče. Například:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Odeberte `Password` dvojici klíč-hodnota z připojovacího řetězce v *appsettings.js*. Například:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Hodnota tajného klíče může být nastavena na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> vlastnost objektu pro dokončení připojovacího řetězce:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Výpis tajných kódů

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets list
```

Objeví se následující výstup:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

V předchozím příkladu dvojtečka v názvech klíčů označuje hierarchii objektů v rámci *secrets.js*.

## <a name="remove-a-single-secret"></a>Odebrání jednoho tajného klíče

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

*secrets.jsaplikace v* souboru se změnila tak, aby se odebral pár klíč-hodnota přidružený ke `MoviesConnectionString` klíči:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Odebrat všechna tajná klíčová

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Z adresáře, ve kterém existuje soubor *. csproj* , spusťte následující příkaz:

```dotnetcli
dotnet user-secrets clear
```

Všechny tajné klíče uživatele pro aplikaci byly z *secrets.jsv* souboru odstraněny:

```json
{}
```

`dotnet user-secrets list`Při spuštění se zobrazí následující zpráva:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Další zdroje

* Informace o přístupu ke Správci tajných kódů ze služby IIS najdete v [tomto problému](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end