---
title: Bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ukládat a načítat citlivých informací jako tajných kódů aplikace během vývoje aplikace ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 1a10c4d035510c689e3eccadc5986df0cc06b71e
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841511"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpečné ukládání tajných kódů aplikace při vývoji v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), a [Scott Addie](https://github.com/scottaddie)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples) ([stažení](xref:index#how-to-download-a-sample))

Tento dokument popisuje postupy pro ukládání a načítání citlivých dat během vývoje aplikace ASP.NET Core. Nikdy ukládání hesel nebo jiných citlivých dat. ve zdrojovém kódu. Tajné klíče v produkčním prostředí by se neměly pro vývoj nebo testování. Můžete ukládat a chránit Azure testovací a produkční tajných kódů pomocí [poskytovatele konfigurace služby Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí je použit k ukládání tajných kódů aplikace v kódu nebo v místních konfiguračních souborech. Proměnné prostředí přepsat hodnoty konfigurace pro všechny zdroje dříve zadanou konfiguraci.

::: moniker range="<= aspnetcore-1.1"

Konfigurace čtení hodnot proměnných prostředí pomocí volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v `Startup` konstruktor:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

Vezměte v úvahu webovou aplikaci ASP.NET Core, ve kterém **jednotlivé uživatelské účty** je zabezpečená. Výchozí připojovací řetězec databáze je součástí projektu *appsettings.json* soubor s klíčem `DefaultConnection`. Výchozí připojovací řetězec je pro LocalDB, který běží v uživatelském režimu a nevyžaduje, aby heslo. Během nasazování aplikací `DefaultConnection` klíče hodnota se dá přepsat hodnotou proměnné prostředí. Proměnná prostředí může ukládat úplný připojovací řetězec s citlivé přihlašovací údaje.

> [!WARNING]
> Proměnné prostředí jsou obecně uložené v nezašifrované prostého textu. Pokud počítači nebo procesu dojde k ohrožení, proměnné prostředí je přístupný nedůvěryhodní. Může vyžadovat další opatření k zamezení zveřejňování těchto tajných kódů uživatelů.

## <a name="secret-manager"></a>Tajný klíč správce

Tajný klíč správce nástroj ukládá citlivých dat během vývoje projektu aplikace ASP.NET Core. V tomto kontextu je část citlivá data tajný kód aplikace. Tajné kódy aplikace jsou uložené v samostatném umístění ve stromu projektu. Tajných kódů aplikace jsou přidružené k určitému projektu nebo sdílet mezi více projekty. Tajných kódů aplikace se změnami do správy zdrojového kódu.

> [!WARNING]
> Nástroj manažera tajných nešifruje uložené tajných kódů a by neměla být zpracována jako důvěryhodné úložiště. Je jenom pro účely vývoje. Klíče a hodnoty jsou uložené v konfiguračním souboru JSON v adresáři profilu uživatele.

## <a name="how-the-secret-manager-tool-works"></a>Jak funguje nástroj tajný klíč správce

Nástroj manažera tajných vyčleňuje podrobnosti implementace, například jak a kde jsou uložené hodnoty. Nástroj můžete použít bez znalosti tyto podrobnosti implementace. Hodnoty jsou uložené v konfiguračním souboru JSON ve složce profilu systému protected Users na místním počítači:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Cesta systému souborů:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

Cesta systému souborů:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

V předchozím cesty k souborům, nahraďte `<user_secrets_id>` s `UserSecretsId` hodnotu zadanou v *.csproj* souboru.

Nemusíte psát kód, který závisí na umístění nebo formátu data uložená pomocí nástroje Správce tajný klíč. Tyto podrobnosti implementace se může změnit. Například hodnoty tajných kódů nejsou zašifrované, ale může být v budoucnosti.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>Nainstalujte nástroj tajný klíč správce

Tajný klíč správce je jako součást balíčku s rozhraní příkazového řádku .NET Core v rozhraní .NET Core SDK 2.1.300 nebo novější. Pro .NET Core SDK verze před 2.1.300 je nutné instalaci nástroje.

> [!TIP]
> Spustit `dotnet --version` z příkazové okno, chcete-li zobrazit číslo nainstalované verze .NET Core SDK.

Pokud se používají sadu .NET Core SDK obsahuje nástroje, zobrazí se upozornění:

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

Nainstalujte [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) balíčku NuGet ve vašem projektu ASP.NET Core. Příklad:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

Spuštěním následujícího příkazu v příkazovém prostředí se ověřit instalaci nástroje:

```console
dotnet user-secrets -h
```

Tajný klíč správce nástroj zobrazí ukázkové využití, možnosti a nápovědy k příkazům:

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> Musí být ve stejném adresáři jako *.csproj* spuštění nástroje, které jsou definovány v souboru *.csproj* souboru `DotNetCliToolReference` elementy.

::: moniker-end

## <a name="enable-secret-storage"></a>Povolit úložiště tajných kódů

Tajný klíč správce nástroj funguje v nastavení konfigurace specifické pro projekt uložené v profilu uživatele.

::: moniker range=">= aspnetcore-3.0"

Obsahuje nástroj Správce tajný kód `init` v .NET Core SDK 3.0.100 příkaz nebo novější. Pokud chcete použít tajné klíče uživatelů, spusťte následující příkaz v adresáři projektu:

```console
dotnet user-secrets init
```

Ve výstupu předchozího příkazu přidá `UserSecretsId` v elementu `PropertyGroup` z *.csproj* souboru. Ve výchozím nastavení vnitřní text `UserSecretsId` je identifikátor GUID. Vnitřní text je volitelný, ale je jedinečné pro projekt.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Pro použití tajných kódů uživatelů, definovat `UserSecretsId` v elementu `PropertyGroup` z *.csproj* souboru. Vnitřní text `UserSecretsId` je volitelný, ale jsou jedinečná pro projekt. Vývojáři obvykle generování identifikátoru GUID pro `UserSecretsId`.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> V sadě Visual Studio, klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **spravovat tajné klíče uživatelů** v místní nabídce. Přidá tento gesta `UserSecretsId` prvek vyplní identifikátor GUID položky *.csproj* souboru.

## <a name="set-a-secret"></a>Nastavte tajného kódu

Definujte tajný kód aplikace, který se skládá z klíče a jeho hodnotu. Tajný kód je přiřazena k projektu `UserSecretsId` hodnotu. Například spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

V předchozím příkladu, který označuje dvojtečka `Movies` je literál s objektu `ServiceApiKey` vlastnost.

Nástroj tajný klíč správce je možné z jiných adresářů příliš. Použití `--project` možnost zadat cestu systému souborů, ve kterém *.csproj* soubor existuje. Příklad:

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Struktura JSON sloučení v sadě Visual Studio

Visual Studio **spravovat tajné klíče uživatelů** gesta otevře *secrets.json* souboru v textovém editoru. Nahraďte obsah *secrets.json* s páry klíč hodnota, které mají být uloženy. Příklad:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON se sloučí po změny prostřednictvím `dotnet user-secrets remove` nebo `dotnet user-secrets set`. Například systém `dotnet user-secrets remove "Movies:ConnectionString"` sbalí `Movies` literálu objektu. Upravený soubor vypadá přibližně takto:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Nastavit víc tajných kódů

Dávku tajné kódy je možné nastavit přesměrujete JSON na `set` příkazu. V následujícím příkladu *Input.JSON vypadá* obsah souboru jsou směrované do `set` příkazu.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Otevřete příkazové okno a spusťte následující příkaz:

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

Otevřete příkazové okno a spusťte následující příkaz:

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Přístup k tajným kódem

[Rozhraní API pro ASP.NET Core konfigurace](xref:fundamentals/configuration/index) poskytuje přístup k tajným kódům tajný klíč správce.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Pokud váš projekt cílí na .NET Framework, nainstalujte [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) balíček NuGet.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

V technologii ASP.NET Core 2.0 nebo novější, uživatelský zdroj konfigurace tajných kódů se automaticky přidá ve vývojovém režimu při volání projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> k inicializaci nové instance hostitele s předem nakonfigurované výchozí hodnoty. `CreateDefaultBuilder` volání <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> při <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> je <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Když `CreateDefaultBuilder` není volána, přidat zdroj konfigurace tajných kódů uživatelů explicitně voláním <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> v `Startup` konstruktoru. Volání `AddUserSecrets` pouze při spuštění aplikace ve vývojovém prostředí, jak je znázorněno v následujícím příkladu:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Nainstalujte [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) balíček NuGet.

Přidat zdroj konfigurace tajných kódů uživatelů pomocí volání <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> v `Startup` konstruktor:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

Tajné klíče uživatelů se dá načíst pomocí `Configuration` rozhraní API:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>Mapování tajných kódů POCO

Mapování literál celého objektu POCO (jednoduchá třída .NET s vlastnostmi) je užitečné pro agregaci související vlastnosti.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Chcete-li namapovat předchozí tajných kódů POCO, použijte `Configuration` rozhraní API [objektu vazby grafu](xref:fundamentals/configuration/index#bind-to-an-object-graph) funkce. Následující kód vytvoří vazbu k vlastní `MovieSettings` POCO a přístupy `ServiceApiKey` hodnota vlastnosti:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

`Movies:ConnectionString` a `Movies:ServiceApiKey` tajných kódů se mapují na odpovídající vlastnosti v `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Náhradní řetězec s tajnými kódy

Není bezpečné ukládání hesel ve formátu prostého textu. Příklad připojovacího řetězce databáze uloženého v *appsettings.json* může zahrnovat heslo pro zadaného uživatele:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Je lépe zabezpečit přístup k uložení hesla jako tajný kód. Příklad:

```console
dotnet user-secrets set "DbPassword" "pass123"
```

Odeberte `Password` páru klíč hodnota z připojovacího řetězce v *appsettings.json*. Příklad:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Hodnota tajného klíče se dá nastavit na <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objektu <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> vlastnost dokončete připojovací řetězec:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>Uvádí tajné klíče.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:

```console
dotnet user-secrets list
```

Zobrazí se následující výstup:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

V předchozím příkladu, dvojtečka v názvu klíče označuje hierarchie objektů v rámci *secrets.json*.

## <a name="remove-a-single-secret"></a>Odebrat tajný kód jednotného

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

Aplikace *secrets.json* změny odebrat páru klíč hodnota, které jsou přidružené k souboru `MoviesConnectionString` klíč:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Spuštění `dotnet user-secrets list` zobrazí následující zprávu:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Odebrání všech tajných kódů

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Spusťte následující příkaz z adresáře, ve kterém *.csproj* soubor existuje:

```console
dotnet user-secrets clear
```

Všechny tajné klíče uživatelů pro aplikaci se odstranily z *secrets.json* souboru:

```json
{}
```

Spuštění `dotnet user-secrets list` zobrazí následující zprávu:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
