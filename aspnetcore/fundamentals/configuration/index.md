---
title: Konfigurace v ASP.NET jádru
author: rick-anderson
description: Přečtěte si, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007155"
---
# <a name="configuration-in-aspnet-core"></a>Konfigurace v ASP.NET jádru

[Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirk Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Konfigurace v ASP.NET Core se provádí pomocí jednoho nebo více [poskytovatelů konfigurace](#cp). Zprostředkovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:

* Soubory nastavení, například *appsettings.json*
* Proměnné prostředí
* Azure Key Vault
* Azure App Configuration
* Argumenty příkazového řádku
* Vlastní zprostředkovatelé, nainstalovaní nebo vytvoření
* Soubory adresářů
* Objekty .NET v paměti

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

<a name="default"></a>

## <a name="default-configuration"></a>Výchozí konfigurace

ASP.NET základní webové aplikace vytvořené pomocí [dotnet new](/dotnet/core/tools/dotnet-new) nebo Visual Studia generují následující kód:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>poskytuje výchozí konfiguraci aplikace v následujícím pořadí:

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá `IConfiguration` existující jako zdroj. Ve výchozím případě konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj pro konfiguraci _aplikace._
1. [appsettings.json](#appsettingsjson) pomocí [zprostředkovatele konfigurace JSON](#file-configuration-provider).
1. *nastavení aplikace.* `Environment` *Json* pomocí [zprostředkovatele konfigurace JSON](#file-configuration-provider). Například *nastavení aplikace*. ***Výroba***. *json* a *appsettings*. ***Vývoj***. *json*.
1. [Tajné kódy aplikací,](xref:security/app-secrets) `Development` když se aplikace spustí v prostředí.
1. Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#evcp).
1. Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line).

Zprostředkovatelé konfigurace, kteří jsou přidáni později přepsat předchozí nastavení klíče. Například pokud `MyKey` je nastavena v *obou appsettings.json* a prostředí, hodnota prostředí se používá. Pomocí výchozích zprostředkovatelů konfigurace [přepíše poskytovatel konfigurace příkazového řádku](#command-line-configuration-provider) všechny ostatní zprostředkovatele.

Další informace `CreateDefaultBuilder`naleznete v [tématu Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).

Následující kód zobrazuje povolené zprostředkovatele konfigurace v pořadí, v jakém byli přidáni:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.json

Zvažte následující soubor *appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> konfigurace načte v následujícím pořadí:

1. *appsettings.json*
1. *nastavení aplikace.* `Environment` *.json* : Například *nastavení aplikace*. ***Výroba***. *json* a *appsettings*. ***Vývoj***. *json* soubory. Verze souboru prostředí je načtena na základě [iHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Další informace naleznete v tématu <xref:fundamentals/environments>.

*appsettings*. `Environment`. *Hodnoty json* přepíší klíče v *souboru appsettings.json*. Ve výchozím nastavení by například:

* Ve vývoji, *appsettings*. ***Vývoj***. *Konfigurace json* přepíše hodnoty nalezené v *souboru appsettings.json*.
* V produkčním prostředí *nastavení aplikací*. ***Výroba***. *Konfigurace json* přepíše hodnoty nalezené v *souboru appsettings.json*. Například při nasazování aplikace do Azure.

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Vazba hierarchických konfiguračních dat pomocí vzoru voleb

Upřednostňovaným způsobem čtení souvisejících hodnot konfigurace je použití [vzoru voleb](xref:fundamentals/configuration/options). Chcete-li například číst následující hodnoty konfigurace:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Vytvořte `PositionOptions` následující třídu:

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Všechny veřejné vlastnosti čtení a zápisu typu jsou vázány. Pole ***nejsou*** vázána.

Následující kód:

* Volá [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) svázat třídu `PositionOptions` do oddílu. `Position`
* Zobrazí `Position` konfigurační data.

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)váže a vrátí zadaný typ. `ConfigurationBinder.Get<T>`může být pohodlnější `ConfigurationBinder.Bind`než použití . Následující kód ukazuje, `ConfigurationBinder.Get<T>` jak `PositionOptions` používat s třídou:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Alternativní přístup při použití ***vzoru možnosti*** je svázat `Position` oddíl a přidat jej do [kontejneru služby vkládání závislostí](xref:fundamentals/dependency-injection). V následujícím kódu `PositionOptions` je přidán do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázán na konfiguraci:

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Pomocí předchozího kódu přečte následující kód možnosti pozice:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Pomocí [výchozí](#default) konfigurace *appsettings.json* a *appsettings.* `Environment` *.json* soubory jsou povoleny s [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Změny provedené v *appsettings.json* a *appsettings.* `Environment` *Soubor JSON* ***po*** spuštění aplikace čte [zprostředkovatel konfigurace JSON](#jcp).

Informace o přidání dalších konfiguračních souborů JSON naleznete v tomto dokumentu na [tématu Zprostředkovatel konfigurace](#jcp) JSON.

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Bezpečnostní a tajný manažer

Pokyny pro konfigurační data:

* Nikdy neuklápějte hesla nebo jiná citlivá data v kódu zprostředkovatele konfigurace nebo v konfiguračních souborech ve formátu prostého textu. Správce [tajných služeb](xref:security/app-secrets) lze použít k ukládání tajných kódů ve vývoji.
* Nepoužívejte produkční tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné kódy mimo projekt tak, aby nemohly být omylem potvrzeny do úložiště zdrojového kódu.

[Ve výchozím nastavení](#default)správce [tajných barev](xref:security/app-secrets) přečte nastavení konfigurace po nastavení *appsettings.json* a *appsettings.* `Environment` *.json*.

Další informace o ukládání hesel nebo jiných citlivých dat:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Obsahuje rady ohledně používání proměnných prostředí k ukládání citlivých dat. Správce tajných barev používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných kódů uživatelů v souboru JSON v místním systému.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné kódy aplikací pro ASP.NET základní aplikace. Další informace naleznete v tématu <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Proměnné prostředí

Pomocí [výchozí](#default) konfigurace <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota proměnné prostředí po přečtení *appsettings.json*, *appsettings.* `Environment` *Json*a [tajný manažer](xref:security/app-secrets). Proto hodnoty klíče číst z prostředí přepsat hodnoty číst z *appsettings.json*, *appsettings.* `Environment` *Json*a tajný manažer.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Následující `set` příkazy:

* Nastavte klíče prostředí a hodnoty [předchozího příkladu v](#appsettingsjson) systému Windows.
* Otestujte nastavení při použití [ukázkového stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). Příkaz `dotnet run` musí být spuštěn v adresáři projektu.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Předchozí nastavení prostředí:

* Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve které byly nastaveny.
* Prohlížeče spuštěné v sadě Visual Studio nebudou číst.

Následující příkazy [setx](/windows-server/administration/windows-commands/setx) lze použít k nastavení klíčů a hodnot prostředí v systému Windows. Na `set` `setx` rozdíl od , nastavení jsou trvalé. `/M`nastaví proměnnou v systémovém prostředí. Pokud `/M` se přepínač nepoužívá, je nastavena proměnná uživatelského prostředí.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Chcete-li otestovat, že předchozí příkazy přepsat *appsettings.json* a *appsettings.* `Environment` *.json*:

* S Visual Studio: Ukončete a restartujte Visual Studio.
* Pomocí příkazového příkazového příkazu: `dotnet run`Spusťte nové příkazové okno a zadejte .

Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem k určení předpony pro proměnné prostředí:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

V předchozím kódu:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`je přidán a [výchozí zprostředkovatelé konfigurace](#default). Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).
* Proměnné prostředí nastavené `MyCustomPrefix_` s předponou přepíší [výchozí zprostředkovatele konfigurace](#default). To zahrnuje proměnné prostředí bez předpony.

Předpona je odstraněna při čtení párů hodnot klíči konfigurace.

Vlastní předponu testují následující příkazy:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty `DOTNET_` `ASPNETCORE_`příkazového řádku s předponou a . A `DOTNET_` `ASPNETCORE_` předpony jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele. Další informace o konfiguraci hostitele a aplikace naleznete v [tématu Obecný hostitel .NET](xref:fundamentals/host/generic-host).

Ve [službě Azure App Service](https://azure.microsoft.com/services/app-service/)vyberte na stránce Nastavení > **konfigurace** nastavení aplikace nové **nastavení.** Nastavení aplikací služby Azure App Service jsou:

* Šifrované v klidovém stavu a přenášené přes šifrovaný kanál.
* Vystaveno jako proměnné prostředí.

Další informace najdete v tématu [Azure Apps: Přepsat konfiguraci aplikací pomocí portálu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Informace o připojovacích řetězech Azure v databázi najdete v tématu [předpony připojovacího řetězce.](#constr)

<a name="clcp"></a>

## <a name="command-line"></a>Příkazový řádek

Při použití [výchozí](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> konfigurace načte konfiguraci z dvojice klíč-hodnota argumentu příkazového řádku za následující zdroje konfigurace:

* *appsettings.json* a *appsettings*. `Environment`. *json* soubory.
* [Tajné klíče aplikací (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

[Ve výchozím nastavení](#default)přepíší hodnoty konfigurace nastavené na hodnotách konfigurace na stavicí příkazového řádku se všemi ostatními poskytovateli konfigurace.

### <a name="command-line-arguments"></a>Argumenty příkazového řádku

Následující příkaz nastavuje `=`klíče a hodnoty pomocí :

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Následující příkaz nastavuje `/`klíče a hodnoty pomocí :

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Následující příkaz nastavuje `--`klíče a hodnoty pomocí :

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Hodnota klíče:

* Musí `=`následovat , nebo klíč musí `--` mít `/` předponu nebo když hodnota následuje mezeru.
* Není vyžadováno, `=` pokud se používá. Například, `MySetting=`.

V rámci stejného příkazu nekombinujte dvojice klíčových hodnot `=` argumentů příkazového řádku, které se používají s dvojicemi klíč-hodnota, které používají mezeru.

### <a name="switch-mappings"></a>Přepnout mapování

Mapování přepínačů umožňuje logiku nahrazení názvu **klíče.** Zadejte slovník switch nahrazení <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.

Při použití slovníku mapování přepínačů je slovník zkontrolován na klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud je klíč příkazového řádku nalezen ve slovníku, hodnota slovníku je předána zpět a nastavte dvojici klíč-hodnota do konfigurace aplikace. Mapování přepínače je vyžadováno pro všechny klávesy příkazového řádku s předponou s jednou pomlčkou (`-`).

Přepnout pravidla klíčů slovníku mapování:

* Přepínače musí `-` `--`začínat písmenem nebo.
* Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.

Chcete-li použít slovník mapování přepínačů, přejděte jej do volání `AddCommandLine`na :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Následující kód zobrazuje hodnoty klíčů pro nahrazené klíče:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Chcete-li otestovat nahrazení klíče, spusťte následující příkaz:

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Poznámka: V `=` současné době nelze použít k nastavení hodnot `-`nahrazení klíče s jednou pomlčkou . Podívejte se na [tento problém GitHub](https://github.com/dotnet/extensions/issues/3059).

Následující příkaz funguje k testování nahrazení klíče:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

U aplikací, které používají mapování `CreateDefaultBuilder` přepínačů, by volání nemělo předávat argumenty. Volání metody neobsahuje mapované přepínače a neexistuje žádný způsob, jak předat slovník mapování přepínačů . `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine` Řešením není předat argumenty, `CreateDefaultBuilder` ale místo toho `ConfigurationBuilder` povolit `AddCommandLine` metodu metody ke zpracování argumentů a slovníku mapování přepínačů.

## <a name="hierarchical-configuration-data"></a>Hierarchická konfigurační data

Konfigurační rozhraní API čte hierarchická konfigurační data sloučením hierarchických dat pomocí oddělovače v konfiguračních klíčích.

[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Upřednostňovaným způsobem čtení hierarchických konfiguračních dat je použití vzoru voleb. Další informace naleznete v [tématu Vazba hierarchických konfiguračních dat](#optpat) v tomto dokumentu.

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici pro oddělení oddílů a podřízených částí v konfiguračních datech. Tyto metody jsou popsány dále v [GetSection, GetChildren a Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Konfigurační klíče a hodnoty

Konfigurační klíče:

* Nerozlišují malá a velká písmena. Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.
* Pokud je klíč a hodnota nastavena ve více než jednom zprostředkovateli konfigurace, použije se hodnota z posledního přidaného zprostředkovatele. Další informace naleznete [v tématu Výchozí konfigurace](#default).
* Hierarchické klíče
  * V rámci rozhraní API konfigurace`:`funguje oddělovač dvojtečky ( ) na všech platformách.
  * V proměnných prostředí oddělovač dvojtečky nemusí fungovat na všech platformách. Dvojité podtržítko , `__`je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:`.
  * V azure key vault hierarchické klíče použít `--` jako oddělovač. [Zprostředkovatel konfigurace úložiště klíčů](xref:security/key-vault-configuration) Azure `--` vault `:` automaticky nahradí při načítání tajných kódů do konfigurace aplikace.
* Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v části [Bind pole.](#boa)

Hodnoty konfigurace:

* Jsou struny.
* Hodnoty null nelze uložit v konfiguraci nebo vázány na objekty.

<a name="cp"></a>

## <a name="configuration-providers"></a>Zprostředkovatelé konfigurace

V následující tabulce jsou uvedeny zprostředkovatelé konfigurace, kteří jsou k dispozici pro ASP.NET aplikace Core.

| Poskytovatel | Poskytuje konfiguraci od |
| -------- | ----------------------------------- |
| [Zprostředkovatel konfigurace služby Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault |
| [Zprostředkovatel konfigurace aplikace Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration |
| [Zprostředkovatel konfigurace příkazového řádku](#clcp) | Parametry příkazového řádku |
| [Vlastní zprostředkovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj |
| [Zprostředkovatel konfigurace proměnných prostředí](#evcp) | Proměnné prostředí |
| [Zprostředkovatel konfigurace souborů](#file-configuration-provider) | Soubory INI, JSON a XML |
| [Zprostředkovatel konfigurace pro jeden soubor](#key-per-file-configuration-provider) | Soubory adresářů |
| [Zprostředkovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti |
| [Tajný správce](xref:security/app-secrets)  | Soubor v adresáři profilu uživatele |

Zdroje konfigurace jsou čteny v pořadí, ve které jsou zadáni jejich zprostředkovatelé konfigurace. Objednejte zprostředkovatele konfigurace v kódu tak, aby vyhovovaly prioritám pro základní zdroje konfigurace, které aplikace vyžaduje.

Typická posloupnost zprostředkovatelů konfigurace je:

1. *appsettings.json*
1. *appsettings*. `Environment`. *json řekl:*
1. [Tajný správce](xref:security/app-secrets)
1. Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#evcp).
1. Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).

Běžnou praxí je přidat zprostředkovatele konfigurace příkazového řádku jako poslední ho v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou jinými zprostředkovateli.

Předchozí posloupnost zprostředkovatelů se používá ve [výchozí konfiguraci](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Předpony připojovacího řetězce

Konfigurační rozhraní API má zvláštní pravidla zpracování pro čtyři proměnné prostředí připojovacího řetězce. Tyto připojovací řetězce se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předčíslími zobrazenými v tabulce se načtou do aplikace `AddEnvironmentVariables`s [výchozí konfigurací](#default) nebo když není zadána žádná předpona .

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace s některou ze čtyř předpon uvedených v tabulce:

* Konfigurační klíč je vytvořen odebráním předpony proměnné`ConnectionStrings`prostředí a přidáním oddílu konfiguračního klíče ( ).
* Je vytvořen nový konfigurační pár klíč-hodnota, `CUSTOMCONNSTR_`který představuje zprostředkovatele připojení k databázi (s výjimkou , který nemá žádného uvedeného zprostředkovatele).

| Klíč proměnných prostředí | Převedený konfigurační klíč | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace nebyla vytvořena.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

Konfigurace <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> zatížení z párů klíč-hodnota souboru JSON.

Přetížení může určit:

* Určuje, zda je soubor volitelný.
* Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.

Uvažujte následující kód:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Předcházející kód:

* Nakonfiguruje zprostředkovatele konfigurace JSON tak, aby načetl soubor *MyConfig.json* následujícími možnostmi:
  * `optional: true`: Soubor je volitelný.
  * `reloadOnChange: true`: Soubor se po uložení změn znovu načte.
* Přečte [výchozí zprostředkovatele konfigurace](#default) před souborem *MyConfig.json.* Nastavení v nastavení přepsání souboru *MyConfig.json* ve výchozím zprostředkovateli konfigurace, včetně [zprostředkovatele konfigurace proměnných prostředí](#evcp) a [zprostředkovatele konfigurace příkazového řádku](#clcp).

Obvykle ***nechcete,*** aby vlastní soubor JSON převažující hodnoty nastavené v [nastavení konfigurace proměnné prostředí a](#evcp) [zprostředkovatele konfigurace příkazového řádku](#clcp).

Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

V předchozím kódu nastavení v *MyConfig.json* a *MyConfig*. `Environment`. *json* soubory:

* Přepište nastavení v *nastavení appsettings.json* a *appsettings*. `Environment`. *json* soubory.
* Jsou přepsány nastavením [ve zprostředkovateli konfigurace proměnných prostředí](#evcp) a [zprostředkovateli konfigurace příkazového řádku](#clcp).

[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyConfig.json:*

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Zprostředkovatel konfigurace souborů

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třída pro načítání konfigurace ze systému souborů. Následující zprostředkovatelé konfigurace `FileConfigurationProvider`jsou odvozeni z :

* [Zprostředkovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#jcp)
* [Zprostředkovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Zprostředkovatel konfigurace INI

Konfigurace <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte z ini souboru klíč-hodnota párů za běhu.

Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

V předchozím kódu nastavení v *MyIniConfig.ini* a *MyIniConfig*. `Environment`. *ini* soubory jsou přepsány nastavenív:

* [Zprostředkovatel konfigurace proměnných prostředí](#evcp)
* [Zprostředkovatel konfigurace příkazového řádku](#clcp).

[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig.ini:*

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Zprostředkovatel konfigurace XML

Konfigurace <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načte z dvojice klíč-hodnota souboru XML za běhu.

Následující kód vymaže všechny zprostředkovatele konfigurace a přidá několik zprostředkovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

V předchozím kódu nastavení v *souborech MyXMLFile.xml* a *MyXMLFile*. `Environment`. soubory *XML* jsou přepsány nastavením v:

* [Zprostředkovatel konfigurace proměnných prostředí](#evcp)
* [Zprostředkovatel konfigurace příkazového řádku](#clcp).

[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile.xml:*

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Opakující se prvky, které používají `name` stejný název prvku práce, pokud atribut se používá k rozlišení prvků:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Atributy lze použít k zadání hodnot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Předchozí konfigurační soubor `value`načte následující klíče pomocí :

* klíč:atribut
* oddíl:klíč:atribut

## <a name="key-per-file-configuration-provider"></a>Zprostředkovatel konfigurace pro jeden soubor

Používá <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> soubory adresáře jako dvojice konfiguračníklíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Zprostředkovatel konfigurace key-per-file se používá ve scénářích hostování dockeru.

Chcete-li aktivovat konfiguraci klíče <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> za soubor, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření v instanci aplikace . Chcete-li `directoryPath` soubory musí být absolutní cestu.

Přetížení povolení s uvedením:

* Delegát, `Action<KeyPerFileConfigurationSource>` který konfiguruje zdroj.
* Určuje, zda je adresář volitelný a zda je cesta k adresáři.

Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.

Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Zprostředkovatel konfigurace paměti

Používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekci v paměti jako dvojice konfiguračníklíč hodnota.

Následující kód přidá kolekci paměti do konfiguračního systému:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

V předchozím kódu `config.AddInMemoryCollection(Dict)` je přidán za [výchozí zprostředkovatele konfigurace](#default). Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).

Příklad objednání zprostředkovatelů konfigurace naleznete v tématu [Zprostředkovatel konfigurace JSON](#jcp).

Viz [Vazba pole](#boa) pro `MemoryConfigurationProvider`jiný příklad pomocí .

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

V předchozím kódu, `NumberKey` pokud není nalezen v konfiguraci, `99` výchozí hodnota se používá.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren a existuje

Příklady, které následují, zvažte následující soubor *MySubsection.json:*

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Následující kód přidá *MySubsection.json* k poskytovatelům konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>Getsection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí pododdíl konfigurace se zadaným klíčem podsekce.

Následující kód vrátí `section1`hodnoty pro :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Následující kód vrátí `section2:subsection0`hodnoty pro :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection`nikdy `null`nevrátí . Pokud odpovídající oddíl nebyl nalezen, `IConfigurationSection` je vrácena prázdná.

Pokud `GetSection` vrátí odpovídající <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddíl, není naplněn. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> a jsou vráceny, pokud oddíl existuje.

### <a name="getchildren-and-exists"></a>GetChildren a existuje

Následující kód volá [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a `section2:subsection0`vrátí hodnoty pro :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Předchozí kód volá [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) ověřit oddíl existuje:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Svázat pole

[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Jakýkoli formát pole, který zveřejňuje segment číselného klíče, je schopen maticové vazby na pole třídy [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)

Vezměme si *MyArray.json* z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Následující kód přidá *myarray.json* k poskytovatelům konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Následující kód přečte konfiguraci a zobrazí hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Předchozí kód vrátí následující výstup:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

V předchozím výstupu index 3 `value40`má hodnotu , odpovídající `"4": "value40",` v *MyArray.json*. Indexy vázaného pole jsou souvislé a nejsou vázány na index konfiguračního klíče. Pořadač konfigurace není schopen spojit nulové hodnoty nebo vytvořit nulové položky ve vázaných objektech.

Následující kód načte konfiguraci `array:entries` metodou <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> rozšíření:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Následující kód přečte konfiguraci `arrayDict` `Dictionary` v a zobrazí hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Předchozí kód vrátí následující výstup:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Index &num;3 v vázaném objektu `array:4` obsahuje konfigurační `value4`data pro konfigurační klíč a jeho hodnotu . Pokud jsou konfigurační data obsahující pole vázána, indexy pole v konfiguračních klíčích se při vytváření objektu používají k iteraci konfiguračních dat. Hodnotu null nelze zachovat v konfiguračních datech a položka s hodnotou null není vytvořena ve vázaném objektu, když pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace &num;pro index 3 může `ArrayExample` být dodána před vazbou &num;na instanci libovolným poskytovatelem konfigurace, který čte dvojici klíč/hodnota indexu 3. Zvažte následující soubor *Value3.json* z ukázkového stažení:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Následující kód zahrnuje konfiguraci pro *Value3.json* a `arrayDict` `Dictionary`:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Následující kód přečte předchozí konfiguraci a zobrazí hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Předchozí kód vrátí následující výstup:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Vlastní zprostředkovatelé konfigurace nejsou nutné k implementaci vazby pole.

## <a name="custom-configuration-provider"></a>Vlastní zprostředkovatel konfigurace

Ukázková aplikace ukazuje, jak vytvořit základní ho poskytovatele konfigurace, který čte dvojice hodnot klíče konfigurace z databáze pomocí [entity Framework (EF)](/ef/core/).

Poskytovatel má následující charakteristiky:

* Ef v paměti databáze se používá pro účely demonstrace. Chcete-li použít databázi, která vyžaduje `ConfigurationBuilder` připojovací řetězec, implementujte sekundární pro zadání připojovacího řetězce od jiného zprostředkovatele konfigurace.
* Zprostředkovatel přečte databázovou tabulku do konfigurace při spuštění. Zprostředkovatel není dotaz databáze na základě pro jeden klíč.
* Opětovné načtení při změně není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.

Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot v databázi.

*Modely/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` a uložte a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, <xref:Microsoft.Extensions.Configuration.IConfigurationSource>která implementuje .

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního zprostředkovatele konfigurace <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>děděním z aplikace . Zprostředkovatel konfigurace inicializuje databázi, když je prázdná. Vzhledem k tomu, [že konfigurační klíče nerozlišují malá a velká písmena](#keys), je slovník používaný k inicializaci databáze vytvořen pomocí porovnávání bez rozlišování velkých a malých písmen ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda `AddEFConfiguration` rozšíření umožňuje přidání zdroje `ConfigurationBuilder`konfigurace do .

*Rozšíření/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak `EFConfigurationProvider` používat vlastní v *Program.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Konfigurace přístupu při spuštění

Následující kód zobrazuje konfigurační data v `Startup` metodách:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Příklad přístupu ke konfiguraci pomocí metod pohodlí při spuštění naleznete v [tématu Spuštění aplikace: Metody pohodlí](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-razor-pages"></a>Přístup ke konfiguraci na stránkách Razor

Následující kód zobrazuje konfigurační data na stránce Razor:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Přístup ke konfiguraci v souboru zobrazení MVC

Následující kód zobrazuje konfigurační data v zobrazení MVC:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Konfigurace hostitele versus aplikace

Před konfigurací a spuštěním aplikace je nakonfigurován a spuštěn *hostitel.* Hostitel je zodpovědný za spuštění aplikace a správu životnosti. Aplikace i hostitel jsou konfigurovány pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. Dvojice klíč-hodnota konfigurace hostitele jsou také zahrnuty v konfiguraci aplikace. Další informace o tom, jak jsou poskytovatelé konfigurace používáni při stavění hostitele a jak zdroje konfigurace ovlivňují konfiguraci hostitele, naleznete v tématu <xref:fundamentals/index#host>.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Výchozí konfigurace hostitele

Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET core verze 2.2 tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Konfigurace hostitele je poskytována od:
  * Proměnné prostředí s `DOTNET_` předponou `DOTNET_ENVIRONMENT`(například) pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona`DOTNET_`( ) je odstraněna při načtení dvojice konfiguračníklíč-hodnota.
  * Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Výchozí konfigurace webového`ConfigureWebHostDefaults`hostitele je vytvořena ( ):
  * Kestrel se používá jako webový server a konfigurován pomocí poskytovatelů konfigurace aplikace.
  * Přidat middleware filtrování hostitelů.
  * Přidat middleware předávaných záhlaví, pokud je proměnná `ASPNETCORE_FORWARDEDHEADERS_ENABLED` prostředí nastavena na . `true`
  * Povolte integraci iis.

## <a name="other-configuration"></a>Jiná konfigurace

Toto téma se bude vymkat *pouze konfiguraci aplikace*. Další aspekty spouštění a hostování aplikací ASP.NET Core jsou konfigurovány pomocí konfiguračních souborů, které nejsou zahrnuty v tomto tématu:

* *launch.json*/*launchSettings.json* jsou konfigurační soubory nástrojů pro vývojové prostředí, popsané:
  * V <xref:fundamentals/environments#development>.
  * V celé sadě dokumentace, kde se soubory používají ke konfiguraci ASP.NET základních aplikací pro scénáře vývoje.
* *Web.config* je konfigurační soubor serveru popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Další informace o migraci konfigurace aplikace z předchozích <xref:migration/proper-to-2x/index#store-configurations>verzí ASP.NET naleznete v tématu .

## <a name="add-configuration-from-an-external-assembly"></a>Přidání konfigurace z externí sestavy

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Zdrojový kód konfigurace](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Konfigurace aplikace v ASP.NET Core je založena na párech klíč-hodnota vytvořené *poskytovateli konfigurace*. Zprostředkovatelé konfigurace čtou konfigurační data do dvojic klíč-hodnota z různých zdrojů konfigurace:

* Azure Key Vault
* Azure App Configuration
* Argumenty příkazového řádku
* Vlastní zprostředkovatelé (nainstalované nebo vytvořené)
* Soubory adresářů
* Proměnné prostředí
* Objekty .NET v paměti
* Soubory nastavení

Balíčky konfigurace pro běžné scénáře zprostředkovatele konfigurace ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuty v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Příklady kódu, které následují a <xref:Microsoft.Extensions.Configuration> v ukázkové aplikaci používají obor názvů:

```csharp
using Microsoft.Extensions.Configuration;
```

*Vzor možností* je rozšíření koncepty konfigurace popsané v tomto tématu. Možnosti používají třídy k reprezentaci skupin souvisejících nastavení. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="host-versus-app-configuration"></a>Konfigurace hostitele versus aplikace

Před konfigurací a spuštěním aplikace je nakonfigurován a spuštěn *hostitel.* Hostitel je zodpovědný za spuštění aplikace a správu životnosti. Aplikace i hostitel jsou konfigurovány pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. Dvojice klíč-hodnota konfigurace hostitele jsou také zahrnuty v konfiguraci aplikace. Další informace o tom, jak jsou poskytovatelé konfigurace používáni při stavění hostitele a jak zdroje konfigurace ovlivňují konfiguraci hostitele, naleznete v tématu <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Jiná konfigurace

Toto téma se bude vymkat *pouze konfiguraci aplikace*. Další aspekty spouštění a hostování aplikací ASP.NET Core jsou konfigurovány pomocí konfiguračních souborů, které nejsou zahrnuty v tomto tématu:

* *launch.json*/*launchSettings.json* jsou konfigurační soubory nástrojů pro vývojové prostředí, popsané:
  * V <xref:fundamentals/environments#development>.
  * V celé sadě dokumentace, kde se soubory používají ke konfiguraci ASP.NET základních aplikací pro scénáře vývoje.
* *Web.config* je konfigurační soubor serveru popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Další informace o migraci konfigurace aplikace z předchozích <xref:migration/proper-to-2x/index#store-configurations>verzí ASP.NET naleznete v tématu .

## <a name="default-configuration"></a>Výchozí konfigurace

Webové aplikace založené na ASP.NET nové šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> [Core dotnet](/dotnet/core/tools/dotnet-new) volají při vytváření hostitele. `CreateDefaultBuilder`poskytuje výchozí konfiguraci aplikace v následujícím pořadí:

Následující text platí pro aplikace používající [webový hostitel](xref:fundamentals/host/web-host). Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)naleznete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).

* Konfigurace hostitele je poskytována od:
  * Proměnné prostředí jsou `ASPNETCORE_` předponou `ASPNETCORE_ENVIRONMENT`(například) pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona`ASPNETCORE_`( ) je odstraněna při načtení dvojice konfiguračníklíč-hodnota.
  * Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Konfigurace aplikace je poskytována od:
  * *appsettings.json* pomocí [zprostředkovatele konfigurace souborů](#file-configuration-provider).
  * *nastavení aplikace. {Environment}.json* pomocí [zprostředkovatele konfigurace souborů](#file-configuration-provider).
  * [Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí pomocí sestavení položky.
  * Proměnné prostředí pomocí [zprostředkovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku pomocí [zprostředkovatele konfigurace příkazového řádku](#command-line-configuration-provider).

## <a name="security"></a>Zabezpečení

K zabezpečení citlivých konfiguračních dat přijměte následující postupy:

* Nikdy neuklápějte hesla nebo jiná citlivá data v kódu zprostředkovatele konfigurace nebo v konfiguračních souborech ve formátu prostého textu.
* Nepoužívejte produkční tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné kódy mimo projekt tak, aby nemohly být omylem potvrzeny do úložiště zdrojového kódu.

Další informace najdete v následujících tématech:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>&ndash; Obsahuje rady ohledně používání proměnných prostředí k ukládání citlivých dat. Správce tajných barev používá zprostředkovatele konfigurace souborů k ukládání tajných kódů uživatelů v souboru JSON v místním systému. Zprostředkovatel konfigurace souborů je popsán dále v tomto tématu.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné kódy aplikací pro ASP.NET základní aplikace. Další informace naleznete v tématu <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hierarchická konfigurační data

Rozhraní CONFIGURATION API je schopno udržovat hierarchická konfigurační data sloučením hierarchických dat pomocí oddělovače v konfiguračních klíčích.

V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou částí:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Při čtení souboru do konfigurace jsou vytvořeny jedinečné klíče pro zachování původní hierarchické datové struktury zdroje konfigurace. Řezy a klíče jsou srovnány se`:`sloučením s použitím dvojtečky ( ) pro zachování původní struktury:

* oddíl0:klíč
* oddíl0:klíč1
* oddíl1:klíč
* oddíl1:klíč1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody jsou k dispozici pro oddělení oddílů a podřízených částí v konfiguračních datech. Tyto metody jsou popsány dále v [GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Zásady

### <a name="sources-and-providers"></a>Zdroje a poskytovatelé

Při spuštění aplikace jsou zdroje konfigurace čteny v pořadí, v jakém jsou zadáni jejich zprostředkovatelé konfigurace.

Zprostředkovatelé konfigurace, kteří implementují zjišťování změn, mají možnost znovu načíst konfiguraci při změně základního nastavení. Například zprostředkovatel konfigurace souborů (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace trezoru klíčů Azure](xref:security/key-vault-configuration) implementují zjišťování změn.

<xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí aplikace (DI).](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Configuration.IConfiguration>lze vstřikovat <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> do Razor <xref:Microsoft.AspNetCore.Mvc.Controller> Pages nebo MVC získat konfiguraci pro třídu.

V následujících příkladech `_config` se toto pole používá pro přístup k hodnotám konfigurace:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Zprostředkovatelé konfigurace nemohou využívat DI, protože není k dispozici, když jsou nastaveny hostitelem.

### <a name="keys"></a>Klíče

Konfigurační klíče přijmout následující konvence:

* Klíče nerozlišují malá a velká písmena. Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.
* Pokud je hodnota pro stejný klíč nastavena stejnými nebo různými poskytovateli konfigurace, je poslední hodnotou nastavenou na klíč použitá hodnota.
* Hierarchické klíče
  * V rámci rozhraní API konfigurace`:`funguje oddělovač dvojtečky ( ) na všech platformách.
  * V proměnných prostředí oddělovač dvojtečky nemusí fungovat na všech platformách. Dvojité podtržítko (`__`) je podporováno všemi platformami a automaticky převedeno na dvojtečku.
  * V azure key vault hierarchické klíče použít `--` (dvě pomlčky) jako oddělovač. Napište kód, který nahradí pomlčky dvojtečkou, když jsou tajné klíče načteny do konfigurace aplikace.
* Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v části [Bind pole.](#bind-an-array-to-a-class)

### <a name="values"></a>Hodnoty

Hodnoty konfigurace přijímají následující konvence:

* Hodnoty jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci nebo vázány na objekty.

## <a name="providers"></a>Poskytovatelé

V následující tabulce jsou uvedeny zprostředkovatelé konfigurace, kteří jsou k dispozici pro ASP.NET aplikace Core.

| Poskytovatel | Poskytuje konfiguraci od&hellip; |
| -------- | ----------------------------------- |
| [Zprostředkovatel konfigurace úložiště klíčů Azure](xref:security/key-vault-configuration) (témata*zabezpečení)* | Azure Key Vault |
| [Zprostředkovatel konfigurace aplikací Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace k Azure) | Azure App Configuration |
| [Zprostředkovatel konfigurace příkazového řádku](#command-line-configuration-provider) | Parametry příkazového řádku |
| [Vlastní zprostředkovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj |
| [Zprostředkovatel konfigurace proměnných prostředí](#environment-variables-configuration-provider) | Proměnné prostředí |
| [Zprostředkovatel konfigurace souborů](#file-configuration-provider) | Soubory (INI, JSON, XML) |
| [Zprostředkovatel konfigurace pro jeden soubor](#key-per-file-configuration-provider) | Soubory adresářů |
| [Zprostředkovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti |
| [Tajné klíče uživatelů (Tajný správce)](xref:security/app-secrets) *(témata zabezpečení)* | Soubor v adresáři profilu uživatele |

Zdroje konfigurace jsou čteny v pořadí, ve které jsou při spuštění určeni jejich poskytovatelé konfigurace. Zprostředkovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá. Objednejte zprostředkovatele konfigurace v kódu tak, aby vyhovovaly prioritám pro základní zdroje konfigurace, které aplikace vyžaduje.

Typická posloupnost zprostředkovatelů konfigurace je:

1. Soubory (*appsettings.json*, *appsettings.{ Environment}.json*, `{Environment}` kde je aktuální hostitelské prostředí aplikace)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Tajné klíče uživatelů (Správce tajných barev)](xref:security/app-secrets) (pouze vývojové prostředí)
1. Proměnné prostředí
1. Argumenty příkazového řádku

Běžnou praxí je umístit zprostředkovatele konfigurace příkazového řádku jako posledního v řadě zprostředkovatelů, aby argumenty příkazového řádku povolily přepsání konfigurace nastavené jinými zprostředkovateli.

Předchozí posloupnost zprostředkovatelů se používá při inicializace nového tvůrce hostitele pomocí `CreateDefaultBuilder`. Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurace tvůrce hostitelů pomocí funkce UseConfiguration

Chcete-li nakonfigurovat <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> tvůrce hostitelů, zavolejte tvůrce hostitele s konfigurací.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>Konfigurace konfigurace

Volání `ConfigureAppConfiguration` při vytváření hostitele určit poskytovatele konfigurace aplikace kromě těch, `CreateDefaultBuilder`které automaticky přidá :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Přepsat předchozí konfiguraci argumenty příkazového řádku

Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat `AddCommandLine` argumenty příkazového řádku, zavolejte poslední:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Odebrat zprostředkovatele přidané createdefaultbuilderem

Chcete-li odebrat `CreateDefaultBuilder`zprostředkovatele přidané uživatelem , nejprve volejte [clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [iConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Využití konfigurace při spuštění aplikace

Konfigurace dodávaná `ConfigureAppConfiguration` do aplikace je k dispozici `Startup.ConfigureServices`během spuštění aplikace, včetně . Další informace naleznete v [části Konfigurace aplikace Access při spuštění.](#access-configuration-during-startup)

## <a name="command-line-configuration-provider"></a>Zprostředkovatel konfigurace příkazového řádku

Načte <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> konfiguraci z dvojice klíč-hodnota argumentu příkazového řádku za běhu.

Chcete-li aktivovat konfiguraci příkazového řádku, je metoda <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> rozšíření volána na instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

`AddCommandLine`je automaticky `CreateDefaultBuilder(string [])` volána, když je volána. Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)

`CreateDefaultBuilder`také zatížení:

* Volitelná konfigurace z *appsettings.json* a *appsettings.{ Prostředí}.json.*
* [Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

`CreateDefaultBuilder`přidá zprostředkovatele konfigurace příkazového řádku jako poslední. Argumenty příkazového řádku předané při přepsání runtime konfigurace nastavené jinými zprostředkovateli.

`CreateDefaultBuilder`funguje, když je hostitel konstruován. Konfigurace příkazového řádku aktivovaná proto `CreateDefaultBuilder` může ovlivnit konfiguraci hostitele.

Pro aplikace založené na šablonách `AddCommandLine` ASP.NET Core `CreateDefaultBuilder`již byla volána aplikací . Chcete-li přidat další zprostředkovatele konfigurace a zachovat možnost přepsat konfiguraci od těchto zprostředkovatelů `ConfigureAppConfiguration` s `AddCommandLine` argumenty příkazového řádku, zavolejte další zprostředkovatele aplikace a volání poslední.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Příklad**

Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Otevřete příkazový řádek v adresáři projektu.
1. Zařazuje příkaz `dotnet run` argument `dotnet run CommandLineKey=CommandLineValue`příkazu .
1. Po spuštění aplikace otevřete prohlížeč v `http://localhost:5000`aplikaci na adrese .
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro `dotnet run`argument příkazového řádku konfigurace k dispozici .

### <a name="arguments"></a>Argumenty

Hodnota musí následovat znaménko rovná se (`=`),`--` `/`nebo klíč musí mít předponu ( nebo ) pokud hodnota následuje za mezerou. Hodnota není vyžadována, pokud se používá znaménko rovná se `CommandLineKey=`(například).

| Předpona klíče               | Příklad                                                |
| ------------------------ | ------------------------------------------------------ |
| Bez předpony                | `CommandLineKey1=value1`                               |
| Dvě pomlčky`--`( )        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Lomítko`/`- )      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

V rámci stejného příkazu nekombinujte dvojice klíčových hodnot argumentů příkazového řádku, které používají znaménko rovná se párům klíč-hodnota, které používají mezeru.

Příklady příkazů:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Přepnout mapování

Mapování přepínačů umožňuje logiku nahrazení názvu klíče. Při ručním vytváření <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>konfigurace s , zadejte slovník <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> switch nahrazení metody.

Při použití slovníku mapování přepínačů je slovník zkontrolován na klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud je klíč příkazového řádku nalezen ve slovníku, hodnota slovníku (nahrazení klíče) je předána zpět a nastavte dvojici klíč-hodnota do konfigurace aplikace. Mapování přepínače je vyžadováno pro všechny klávesy příkazového řádku s předponou s jednou pomlčkou (`-`).

Přepnout pravidla klíčů slovníku mapování:

* Přepínače musí začínat pomlčkou`-`(`--`) nebo dvojitou pomlčkou ( ).
* Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.

Vytvořte slovník mapování přepínačů. V následujícím příkladu jsou vytvořeny dvě mapování přepínačů:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Když je hostitel vytvořen, volejte pomocí `AddCommandLine` slovníku mapování přepínačů:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

U aplikací, které používají mapování `CreateDefaultBuilder` přepínačů, by volání nemělo předávat argumenty. Volání metody neobsahuje mapované přepínače a neexistuje žádný způsob, jak předat slovník mapování přepínačů . `CreateDefaultBuilder` `CreateDefaultBuilder` `AddCommandLine` Řešením není předat argumenty, `CreateDefaultBuilder` ale místo toho `ConfigurationBuilder` povolit `AddCommandLine` metodu metody pro zpracování argumentů i slovníku mapování přepínačů.

Po vytvoření slovníku mapování přepínačů obsahuje data zobrazená v následující tabulce.

| Klíč       | Hodnota             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Pokud se při spuštění aplikace používají klávesy mapované přepínačem, konfigurace obdrží hodnotu konfigurace na klíči dodaném slovníkem:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.

| Klíč               | Hodnota    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Zprostředkovatel konfigurace proměnných prostředí

Konfigurace <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> zatížení z dvojice proměnné klíč-hodnota prostředí za běhu.

Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> proměnných prostředí, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření na instanci aplikace .

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Služba Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavení proměnných prostředí na webu Azure Portal, které můžou přepsat konfiguraci aplikace pomocí zprostředkovatele konfigurace proměnných prostředí. Další informace najdete v tématu [Azure Apps: Přepsat konfiguraci aplikací pomocí portálu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables`se používá k načtení `ASPNETCORE_` proměnných prostředí s předponou pro [konfiguraci hostitele,](#host-versus-app-configuration) když je inicializován nový tvůrce hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je volán. Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)

`CreateDefaultBuilder`také zatížení:

* Konfigurace aplikace z nepředponožených proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.
* Volitelná konfigurace z *appsettings.json* a *appsettings.{ Prostředí}.json.*
* [Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Zprostředkovatel konfigurace proměnných prostředí se nazývá po konfiguraci je vytvořen z tajných kódů uživatelů a soubory *nastavení aplikací.* Volání zprostředkovatele v této pozici umožňuje proměnné prostředí číst za běhu přepsat konfiguraci nastavenou tajnými kódy uživatelů a soubory *nastavení aplikací.*

Chcete-li zajistit konfiguraci aplikace z dalších proměnných `ConfigureAppConfiguration` prostředí, zavolejte další zprostředkovatele aplikace a zavolejte `AddEnvironmentVariables` s předponou:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Volání `AddEnvironmentVariables` poslední povolit proměnné prostředí s danou předponou přepsat hodnoty od jiných poskytovatelů.

**Příklad**

Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje volání `AddEnvironmentVariables`.

1. Spusťte ukázkovou aplikaci. Otevřete prohlížeč do `http://localhost:5000`aplikace na adrese .
1. Všimněte si, že výstup obsahuje dvojici `ENVIRONMENT`klíč-hodnota pro proměnnou prostředí . Hodnota odráží prostředí, ve kterém je aplikace `Development` spuštěna, obvykle při spuštění místně.

Chcete-li zachovat seznam proměnných prostředí vykreslených aplikací krátký, aplikace filtruje proměnné prostředí. Podívejte se na ukázkový soubor *Pages/Index.cshtml.cs.*

Chcete-li zpřístupnit všechny proměnné prostředí, které `FilteredConfiguration` jsou v aplikaci k dispozici, změňte v *stránce/index.cshtml.cs* následující:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Předpony

Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při poskytování `AddEnvironmentVariables` předpony metody. Chcete-li například filtrovat proměnné `CUSTOM_`prostředí na předponě , zajistěte předponu zprostředkovateli konfigurace:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Předpona je odstraněna při vytvoření dvojice konfigurační klíč hodnota.

Při vytvoření tvůrce hostitele je konfigurace hostitele poskytována proměnnými prostředí. Další informace o předponě použité pro tyto proměnné prostředí naleznete v části [Výchozí konfigurace.](#default-configuration)

**Předpony připojovacího řetězce**

Konfigurační rozhraní API má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacího řetězce, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předčíslími zobrazenými v tabulce se načtou do aplikace, pokud není zadána `AddEnvironmentVariables`žádná předpona .

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace s některou ze čtyř předpon uvedených v tabulce:

* Konfigurační klíč je vytvořen odebráním předpony proměnné`ConnectionStrings`prostředí a přidáním oddílu konfiguračního klíče ( ).
* Je vytvořen nový konfigurační pár klíč-hodnota, `CUSTOMCONNSTR_`který představuje zprostředkovatele připojení k databázi (s výjimkou , který nemá žádného uvedeného zprostředkovatele).

| Klíč proměnných prostředí | Převedený konfigurační klíč | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace nebyla vytvořena.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |

**Příklad**

Na serveru je vytvořena vlastní proměnná prostředí připojovacího řetězce:

* Název &ndash;`CUSTOMCONNSTR_ReleaseDB`
* Hodnota &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Pokud `IConfiguration` je vložen a přiřazen `_config`k poli s názvem , přečtěte si hodnotu:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Zprostředkovatel konfigurace souborů

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třída pro načítání konfigurace ze systému souborů. Pro konkrétní typy souborů jsou vyhrazeni následující poskytovatelé konfigurace:

* [Zprostředkovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#json-configuration-provider)
* [Zprostředkovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Zprostředkovatel konfigurace INI

Konfigurace <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte z ini souboru klíč-hodnota párů za běhu.

Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> souboru INI, zavolejte metodu přípony v instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Dvojtečka může být použita jako oddělovač oddílů v konfiguraci souboru INI.

Přetížení povolení s uvedením:

* Určuje, zda je soubor volitelný.
* Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.
* Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.

Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Obecný příklad konfiguračního souboru INI:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Předchozí konfigurační soubor `value`načte následující klíče pomocí :

* oddíl0:klíč
* oddíl0:klíč1
* section1:podsekce:klíč
* section2:podsekce0:klíč
* section2:podsekce1:klíč

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

Konfigurace <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> zatížení z dvojice klíč-hodnota souboru JSON za běhu.

Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> souboru JSON, zavolejte metodu rozšíření na instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení povolení s uvedením:

* Určuje, zda je soubor volitelný.
* Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.
* Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.

`AddJsonFile`je automaticky volána dvakrát při inicializování nového tvůrce hostitele pomocí `CreateDefaultBuilder`aplikace . Metoda je volána k načtení konfigurace z:

* *appsettings.json* &ndash; Tento soubor se čte jako první. Verze souboru prostředí může přepsat hodnoty poskytované souborem *appsettings.json.*
* *nastavení aplikace. {Environment}.json* &ndash; Verze souboru prostředí je načtena na základě [iHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Další informace naleznete v části [Výchozí konfigurace.](#default-configuration)

`CreateDefaultBuilder`také zatížení:

* Proměnné prostředí.
* [Tajné klíče uživatelů (Správce tajných kódů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Jako první je zřízen zprostředkovatel konfigurace JSON. Proto tajné klíče uživatelů, proměnné prostředí a argumenty příkazového řádku přepsat konfiguraci nastavenou soubory *nastavení aplikace.*

Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace pro soubory jiné než *appsettings.json* a *appsettings.{ Životní prostředí}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Příklad**

Ukázková aplikace využívá statickou `CreateDefaultBuilder` metodu pohodlí k sestavení hostitele, která zahrnuje dvě volání `AddJsonFile`:

* První volání `AddJsonFile` načte konfiguraci z *appsettings.json*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Druhé volání `AddJsonFile` načte konfiguraci z *nastavení aplikace.{ Prostředí}.json*. Pro *nastavení aplikace. Development.json* v ukázkové aplikaci, načte se následující soubor:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Spusťte ukázkovou aplikaci. Otevřete prohlížeč do `http://localhost:5000`aplikace na adrese .
1. Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace. Úroveň protokolu pro `Logging:LogLevel:Default` klíč `Debug` je při spuštění aplikace ve vývojovém prostředí.
1. Spusťte ukázkovou aplikaci znovu v produkčním prostředí:
   1. Otevřete soubor *Properties/launchSettings.json.*
   1. V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné `Production`prostředí na .
   1. Uložte soubor a spusťte aplikaci v `dotnet run` příkazovém prostředí.
1. Nastavení v *nastavení aplikace. Soubor Development.json* již nepřepisuje nastavení v *souboru appsettings.json*. Úroveň protokolu pro `Logging:LogLevel:Default` klíč `Warning`je .

### <a name="xml-configuration-provider"></a>Zprostředkovatel konfigurace XML

Konfigurace <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načte z dvojice klíč-hodnota souboru XML za běhu.

Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> přípony v instanci aplikace <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení povolení s uvedením:

* Určuje, zda je soubor volitelný.
* Určuje, zda je konfigurace znovu načtena, pokud se soubor změní.
* Slouží <xref:Microsoft.Extensions.FileProviders.IFileProvider> k přístupu k souboru.

Kořenový uzel konfiguračního souboru je při vytvoření dvojice konfiguračního klíče a hodnoty ignorován. Nezadávejte v souboru definici typu dokumentu (DTD) ani obor názvů.

Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Konfigurační soubory XML mohou pro oddíly s opakováním používat odlišné názvy prvků:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Předchozí konfigurační soubor `value`načte následující klíče pomocí :

* oddíl0:klíč
* oddíl0:klíč1
* oddíl1:klíč
* oddíl1:klíč1

Opakující se prvky, které používají `name` stejný název prvku práce, pokud atribut se používá k rozlišení prvků:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Předchozí konfigurační soubor `value`načte následující klíče pomocí :

* oddíl:oddíl0:klíč:klíč0
* oddíl:oddíl0:klíč:klíč1
* oddíl:section1:klíč:klíč0
* oddíl:section1:klíč:klíč1

Atributy lze použít k zadání hodnot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Předchozí konfigurační soubor `value`načte následující klíče pomocí :

* klíč:atribut
* oddíl:klíč:atribut

## <a name="key-per-file-configuration-provider"></a>Zprostředkovatel konfigurace pro jeden soubor

Používá <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> soubory adresáře jako dvojice konfiguračníklíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Zprostředkovatel konfigurace key-per-file se používá ve scénářích hostování dockeru.

Chcete-li aktivovat konfiguraci klíče <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> za soubor, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření v instanci aplikace . Chcete-li `directoryPath` soubory musí být absolutní cestu.

Přetížení povolení s uvedením:

* Delegát, `Action<KeyPerFileConfigurationSource>` který konfiguruje zdroj.
* Určuje, zda je adresář volitelný a zda je cesta k adresáři.

Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.

Volání `ConfigureAppConfiguration` při vytváření hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Zprostředkovatel konfigurace paměti

Používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekci v paměti jako dvojice konfiguračníklíč hodnota.

Chcete-li aktivovat konfiguraci <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> kolekce v paměti, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zavolejte metodu rozšíření na instanci aplikace .

Zprostředkovatel konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.

Volání `ConfigureAppConfiguration` při vytváření hostitele určit konfiguraci aplikace.

V následujícím příkladu je vytvořen konfigurační slovník:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Slovník se používá s voláním k `AddInMemoryCollection` zajištění konfigurace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce. Přetížení přijímá výchozí hodnotu.

Následující příklad:

* Extrahuje hodnotu řetězce z `NumberKey`konfigurace pomocí klíče . Pokud `NumberKey` není nalezen v konfiguračních `99` klíčích, použije se výchozí hodnota.
* Zadá hodnotu `int`jako .
* Uloží hodnotu `NumberConfig` ve vlastnosti pro použití na stránce.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren a existuje

Příklady, které následují, zvažte následující soubor JSON. Čtyři klíče se nacházejí ve dvou částech, z nichž jedna obsahuje dvojici podsekcí:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Při čtení souboru do konfigurace jsou vytvořeny následující jedinečné hierarchické klíče pro uložení hodnot konfigurace:

* oddíl0:klíč
* oddíl0:klíč1
* oddíl1:klíč
* oddíl1:klíč1
* section2:podsekce0:key0
* section2:podsekce0:klíč1
* section2:podsekce1:klíč
* section2:podsekce1:klíč1

### <a name="getsection"></a>Getsection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje podsekci konfigurace se zadaným klíčem podsekce.

Chcete-li <xref:Microsoft.Extensions.Configuration.IConfigurationSection> vrátit obsahující pouze dvojice klíč `section1`hodnota `GetSection` v aplikaci , volání a zadání názvu oddílu:

```csharp
var configSection = _config.GetSection("section1");
```

Nemá `configSection` hodnotu, pouze klíč a cestu.

Podobně chcete-li získat hodnoty `section2:subsection0`pro `GetSection` klíče v , volání a zadání cesty oddílu:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`nikdy `null`nevrátí . Pokud odpovídající oddíl nebyl nalezen, `IConfigurationSection` je vrácena prázdná.

Pokud `GetSection` vrátí odpovídající <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddíl, není naplněn. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> a jsou vráceny, pokud oddíl existuje.

### <a name="getchildren"></a>GetChildren

Volání [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) na `section2` `IEnumerable<IConfigurationSection>` získá, který zahrnuje:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Existuje

Pomocí [configurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) zjistěte, zda existuje oddíl konfigurace:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Vzhledem k `sectionExists` příkladu dat `false` je, `section2:subsection2` protože v konfiguračních datech není oddíl.

## <a name="bind-to-an-object-graph"></a>Vazba na objektový graf

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopen spoje celého grafu objektů POCO. Stejně jako u vazby jednoduchý objekt, jsou vázány pouze veřejné vlastnosti čtení a zápisu.

Ukázka obsahuje `TvShow` model, jehož objektový graf obsahuje `Metadata` a `Actors` třídy *(Models/TvShow.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Ukázková aplikace obsahuje soubor *tvshow.xml* obsahující konfigurační data:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Konfigurace je vázána `TvShow` na celý `Bind` objekt grafu s metodou. Vázaná instance je přiřazena vlastnosti pro vykreslování:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)váže a vrátí zadaný typ. `Get<T>`je pohodlnější než `Bind`použití . Následující kód ukazuje, `Get<T>` jak používat s předchozím příkladem:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

*Ukázková aplikace ukazuje koncepty vysvětlené v této části.*

Podporuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> vazby pole na objekty pomocí indexů pole v konfiguračních klíčích. Jakýkoli formát pole, který zveřejňuje`:0:`číselný &hellip; `:{n}:`segment klíče ( , `:1:`, ) je schopen matice vazby na pole třídy POCO.

> [!NOTE]
> Vazba je poskytována úmluvou. Vlastní zprostředkovatelé konfigurace nejsou nutné k implementaci vazby pole.

**Zpracování pole v paměti**

Zvažte konfigurační klíče a hodnoty uvedené v následující tabulce.

| Klíč             | Hodnota  |
| :-------------: | :----: |
| pole:položky:0 | hodnota0 |
| pole:položky:1 | hodnota1 |
| pole:položky:2 | hodnota2 |
| pole:položky:4 | hodnota4 |
| pole:položky:5 | hodnota5 |

Tyto klíče a hodnoty se načítají v ukázkové aplikaci pomocí zprostředkovatele konfigurace paměti:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Pole přeskočí hodnotu &num;indexu 3. Konfigurační pořadač není schopen vazby nula hodnoty nebo vytváření nulových položek v vázané objekty, což se stane jasné v okamžiku, kdy je prokázánvýsledek vazby tohoto pole na objekt.

V ukázkové aplikaci je k dispozici třída POCO pro uložení vázaných konfiguračních dat:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Konfigurační data jsou vázána na objekt:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)syntaxe lze také použít, což má za následek kompaktnější kód:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Vázaný objekt, instance `ArrayExample`aplikace , přijímá data pole z konfigurace.

| `ArrayExample.Entries`Index | `ArrayExample.Entries`Hodnotu |
| :--------------------------: | :--------------------------: |
| 0                            | hodnota0                       |
| 1                            | hodnota1                       |
| 2                            | hodnota2                       |
| 3                            | hodnota4                       |
| 4                            | hodnota5                       |

Index &num;3 v vázaném objektu `array:4` obsahuje konfigurační `value4`data pro konfigurační klíč a jeho hodnotu . Pokud jsou konfigurační data obsahující pole vázána, indexy pole v konfiguračních klíčích se při vytváření objektu používají pouze k iteraci konfiguračních dat. Hodnotu null nelze zachovat v konfiguračních datech a položka s hodnotou null není vytvořena ve vázaném objektu, když pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace &num;pro index 3 může `ArrayExample` být dodána před vazbou na instanci libovolným poskytovatelem konfigurace, který vytvoří správný pár klíč-hodnota v konfiguraci. Pokud ukázka zahrnovala dalšího zprostředkovatele konfigurace JSON s `ArrayExample.Entries` chybějícím párem klíč-hodnota, odpovídá kompletnímu konfiguračnímu poli:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

V `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Dvojice klíč-hodnota zobrazená v tabulce je načtena do konfigurace.

| Klíč             | Hodnota  |
| :-------------: | :----: |
| pole:položky:3 | hodnota3 |

Pokud `ArrayExample` je instance třídy vázána poté, co zprostředkovatel &num;konfigurace `ArrayExample.Entries` JSON zahrnuje položku pro index 3, pole obsahuje hodnotu.

| `ArrayExample.Entries`Index | `ArrayExample.Entries`Hodnotu |
| :--------------------------: | :--------------------------: |
| 0                            | hodnota0                       |
| 1                            | hodnota1                       |
| 2                            | hodnota2                       |
| 3                            | hodnota3                       |
| 4                            | hodnota4                       |
| 5                            | hodnota5                       |

**Zpracování pole JSON**

Pokud soubor JSON obsahuje pole, konfigurační klíče jsou vytvořeny pro prvky pole s indexem oddílu založeném na nule. V následujícím konfiguračním souboru `subsection` je pole:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:

| Klíč                     | Hodnota  |
| ----------------------- | :----: |
| json_array:klíč          | valueA |
| json_array:podsekce:0 | valueB |
| json_array:podsekce:1 | valueC |
| json_array:podsekce:2 | Hodnotami |

V ukázkové aplikaci je k dispozici následující třída POCO, která sváže dvojice hodnot konfiguračního klíče a hodnoty:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Po vazbě podrží `JsonArrayExample.Key` hodnotu `valueA`. Hodnoty podsekce jsou uloženy ve vlastnosti pole POCO `Subsection`.

| `JsonArrayExample.Subsection`Index | `JsonArrayExample.Subsection`Hodnotu |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | Hodnotami                              |

## <a name="custom-configuration-provider"></a>Vlastní zprostředkovatel konfigurace

Ukázková aplikace ukazuje, jak vytvořit základní ho poskytovatele konfigurace, který čte dvojice hodnot klíče konfigurace z databáze pomocí [entity Framework (EF)](/ef/core/).

Poskytovatel má následující charakteristiky:

* Ef v paměti databáze se používá pro účely demonstrace. Chcete-li použít databázi, která vyžaduje `ConfigurationBuilder` připojovací řetězec, implementujte sekundární pro zadání připojovacího řetězce od jiného zprostředkovatele konfigurace.
* Zprostředkovatel přečte databázovou tabulku do konfigurace při spuštění. Zprostředkovatel není dotaz databáze na základě pro jeden klíč.
* Opětovné načtení při změně není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.

Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot v databázi.

*Modely/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` a uložte a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, <xref:Microsoft.Extensions.Configuration.IConfigurationSource>která implementuje .

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního zprostředkovatele konfigurace <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>děděním z aplikace . Zprostředkovatel konfigurace inicializuje databázi, když je prázdná.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda `AddEFConfiguration` rozšíření umožňuje přidání zdroje `ConfigurationBuilder`konfigurace do .

*Rozšíření/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak `EFConfigurationProvider` používat vlastní v *Program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Konfigurace přístupu při spuštění

Vstříkněte `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v aplikaci `Startup.ConfigureServices`. Chcete-li `Startup.Configure`získat přístup `IConfiguration` ke konfiguraci v aplikaci , buď vložte přímo do metody nebo použijte instanci z konstruktoru:

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Příklad přístupu ke konfiguraci pomocí metod pohodlí při spuštění naleznete v [tématu Spuštění aplikace: Metody pohodlí](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Přístup ke konfiguraci na stránce Razor Pages nebo mvc zobrazení

Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo v zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([C# reference: using directive)](/dotnet/csharp/language-reference/keywords/using-directive)pro [obor názvů Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> je do stránky nebo zobrazení.

Na stránce Razor Pages:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

V zobrazení MVC:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Přidání konfigurace z externí sestavy

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidávání vylepšení do aplikace při spuštění z externího `Startup` sestavení mimo třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/options>

::: moniker-end
