---
title: Konfigurace v ASP.NET Core
author: rick-anderson
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: ed40800f3e363cc3fd6f57492d76a42095e5b97b
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712555"
---
# <a name="configuration-in-aspnet-core"></a>Konfigurace v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Konfigurace v ASP.NET Core provádí použití jednoho nebo více [poskytovatelů konfigurace](#cp). Poskytovatelé konfigurace čtou konfigurační data z párů klíč-hodnota pomocí různých zdrojů konfigurace:

* Soubory nastavení, například *appsettings.jszapnuto*
* Proměnné prostředí
* Azure Key Vault
* Azure App Configuration
* Argumenty příkazového řádku
* Vlastní zprostředkovatelé, nainstalované nebo vytvořené
* Soubory adresáře
* Objekty .NET v paměti

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Výchozí konfigurace

ASP.NET Core webové aplikace vytvořené pomocí [dotnet New](/dotnet/core/tools/dotnet-new) nebo sady Visual Studio generují následující kód:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Přidá existující `IConfiguration` jako zdroj. Ve výchozím případu konfigurace přidá konfiguraci [hostitele](#hvac) a nastaví ji jako první zdroj konfigurace _aplikace_ .
1. [appsettings.js](#appsettingsjson) používání [poskytovatele konfigurace JSON](#file-configuration-provider).
1. *appSettings.* `Environment` *. JSON* s použitím [poskytovatele konfigurace JSON](#file-configuration-provider). Například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. *JSON*.
1. [Tajné kódy aplikace](xref:security/app-secrets) v případě, že aplikace běží v `Development` prostředí
1. Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).
1. Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line).

Poskytovatelé konfigurace, kteří jsou přidáni později, přepíší předchozí nastavení klíče. Například pokud `MyKey` je nastavena v *appsettings.jsv* a prostředí, je použita hodnota prostředí. Pomocí výchozích zprostředkovatelů konfigurace přepíše  [Poskytovatel konfigurace příkazového řádku](#clcp) všechny ostatní poskytovatele.

Další informace o najdete v `CreateDefaultBuilder` tématu [výchozí nastavení tvůrce](xref:fundamentals/host/generic-host#default-builder-settings).

Následující kód zobrazuje povolené poskytovatele konfigurace v pořadí, v jakém byly přidány:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.jsna

V souboru zvažte následující *appsettings.js* :

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Výchozí <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načtení konfigurace v následujícím pořadí:

1. *appsettings.jsna*
1. *appSettings.* `Environment` *. JSON* : například *appSettings*. ***Provozní***prostředí. *JSON* a *appSettings*. ***Vývoj***. soubory *JSON* . Verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Další informace naleznete v tématu <xref:fundamentals/environments>.

*appSettings*. `Environment` .. hodnoty *JSON* přepisují klíče v *appsettings.js*. Ve výchozím nastavení by například:

* Ve vývoji, *appSettings*. ***Vývoj***. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*.
* V produkčním prostředí, *appSettings*. ***Provozní***prostředí. konfigurace *JSON* Přepisuje hodnoty nalezené v *appsettings.js*. Například při nasazení aplikace do Azure.

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Vytvoření vazby hierarchických konfiguračních dat pomocí vzoru možností

[!INCLUDE[](~/includes/bind.md)]

Pomocí [výchozí](#default) konfigurace *appsettings.jsna* a *appSettings.* `Environment` soubory *. JSON* jsou povolené pomocí [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Změny provedené v *appsettings.jsv* a *appSettings.* `Environment` soubor *. JSON* ***po*** spuštění aplikace si přečte [Poskytovatel konfigurace JSON](#jcp).

Informace o přidání dalších konfiguračních souborů JSON najdete v části [Poskytovatel konfigurace JSON](#jcp) v tomto dokumentu.

## <a name="combining-service-collection"></a>Kombinování kolekce služeb

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Správce zabezpečení a tajného klíče

Pokyny pro konfigurační data:

* Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem. [Správce tajného klíče](xref:security/app-secrets) se dá použít k ukládání tajných kódů do vývoje.
* Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.

Ve [výchozím nastavení](#default)přečte [správce tajných klíčů](xref:security/app-secrets) nastavení konfigurace po *appsettings.jsv* a *appSettings.* `Environment` *. JSON*.

Další informace o ukládání hesel nebo jiných citlivých dat:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat. Správce tajného kódu používá [zprostředkovatele konfigurace souborů](#fcp) k ukládání tajných klíčů uživatele do souboru JSON v místním systému.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace. Další informace naleznete v tématu <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Proměnné prostředí

Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z proměnných prostředí s proměnnou klíč-hodnota po přečtení *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a [správce tajných klíčů](xref:security/app-secrets). Proto hodnoty klíčů načtené z prostředí přepisují hodnoty načtené z *appsettings.jsna*, *appSettings.* `Environment` *. JSON*a správce tajných klíčů.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Následující `set` příkazy:

* Nastavte klíče prostředí a hodnoty v [předchozím příkladu](#appsettingsjson) ve Windows.
* Při použití [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)otestujte nastavení. `dotnet run`Příkaz musí být spuštěn v adresáři projektu.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Předchozí nastavení prostředí:

* Jsou nastaveny pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.
* Nebudou přečteny pomocí prohlížečů spuštěných v aplikaci Visual Studio.

K nastavení klíčů a hodnot prostředí ve Windows se dají použít následující příkazy [setx](/windows-server/administration/windows-commands/setx) . Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá. `/M` nastaví proměnnou v prostředí systému. Pokud se `/M` přepínač nepoužívá, je nastavena proměnná prostředí uživatele.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Chcete-li otestovat, zda předchozí příkazy přepisují *appsettings.jsv* a *appSettings.* `Environment` *. JSON*:

* Se sadou Visual Studio: Ukončete a restartujte Visual Studio.
* Pomocí rozhraní příkazového řádku: spusťte nové příkazové okno a zadejte `dotnet run` .

Volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> s řetězcem pro určení předpony pro proměnné prostředí:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

V předchozím kódu:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se přidá za [výchozí poskytovatelé konfigurace](#default). Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).
* Proměnné prostředí nastavené s `MyCustomPrefix_` předponou přepíšou [výchozí poskytovatele konfigurace](#default). To zahrnuje proměnné prostředí bez předpony.

Pokud jsou načteny páry klíč-hodnota konfigurace, je předpona odstraněna.

Vlastní předponu otestujete následujícími příkazy:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[Výchozí konfigurace](#default) načte proměnné prostředí a argumenty příkazového řádku s předponou `DOTNET_` a `ASPNETCORE_` . `DOTNET_` `ASPNETCORE_` Předpony a jsou používány ASP.NET Core pro [konfiguraci hostitele a aplikace](xref:fundamentals/host/generic-host#host-configuration), ale ne pro konfiguraci uživatele. Další informace o konfiguraci hostitele a aplikace najdete v tématu [obecný hostitel .NET](xref:fundamentals/host/generic-host).

V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** . Azure App Service nastavení aplikace:

* Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.
* Vystaveno jako proměnné prostředí.

Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Informace o připojovacích řetězcích Azure Database najdete v tématu [předpony připojovacího řetězce](#constr) .

### <a name="environment-variables-set-in-launchsettingsjson"></a>Proměnné prostředí nastavené v launchSettings.jszapnuté

Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.

<a name="clcp"></a>

## <a name="command-line"></a>Příkazový řádek

Použijete-li [výchozí](#default) konfiguraci, <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načte konfiguraci z dvojice klíč-hodnota argumentu klíč-hodnota po následujících zdrojích konfigurace:

* *appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .
* [Tajné kódy aplikací (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

Ve [výchozím nastavení](#default)jsou konfigurační hodnoty nastavené pro přepisy hodnot konfigurace na příkazovém řádku nastavené se všemi ostatními zprostředkovateli konfigurace.

### <a name="command-line-arguments"></a>Argumenty příkazového řádku

Následující příkaz nastaví klíče a hodnoty pomocí `=` :

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Následující příkaz nastaví klíče a hodnoty pomocí `/` :

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Následující příkaz nastaví klíče a hodnoty pomocí `--` :

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Hodnota klíče:

* Musí následovat po `=` , nebo klíč musí mít předponu `--` nebo, `/` Pokud se hodnota řídí mezerou.
* Není vyžadováno `=` , pokud je použit. Například, `MySetting=`.

V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které `=` se používají s páry klíč-hodnota, které používají mezeru.

### <a name="switch-mappings"></a>Mapování přepínačů

Mapování přepínačů povolují logiku nahrazení názvu **klíče** . Poskytněte slovník pro nahrazení přepínačů v <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metodě.

Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota Dictionary zpátky, aby se v konfiguraci aplikace nastavil pár klíč-hodnota. Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).

Pravidla klíče slovníku mapování přepínačů:

* Přepínače musí začínat na `-` nebo `--` .
* Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.

Chcete-li použít slovník mapování přepínačů, předejte jej do volání `AddCommandLine` :

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Následující kód ukazuje klíčové hodnoty pro nahrazené klíče:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Následující příkaz funguje pro otestování nahrazení klíče:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty. `CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` . Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty i slovník mapování přepínačů.

## <a name="hierarchical-configuration-data"></a>Hierarchická konfigurační data

Rozhraní API pro konfiguraci čte hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.

[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *appsettings.js* souboru:

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Upřednostňovaným způsobem, jak číst hierarchická konfigurační data, je použít vzor možností. Další informace najdete v tématu [vázání hierarchických konfiguračních dat](#optpat) v tomto dokumentu.

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech. Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Konfigurační klíče a hodnoty

Konfigurační klíče:

* Rozlišují se malá a velká písmena. Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.
* Pokud je klíč a hodnota nastavená ve více poskytovatelích konfigurace, použije se hodnota z posledního přidaného zprostředkovatele. Další informace najdete v tématu [výchozí konfigurace](#default).
* Hierarchické klíče
  * V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.
  * V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách. Dvojité podtržítko, `__` , je podporováno všemi platformami a je automaticky převedeno na dvojtečku `:` .
  * V Azure Key Vault hierarchické klíče používají `--` jako oddělovač. [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) se `--` `:` při načtení tajných klíčů do konfigurace aplikace automaticky nahradí.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#boa) .

Konfigurační hodnoty:

* Jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.

<a name="cp"></a>

## <a name="configuration-providers"></a>Poskytovatelé konfigurace

V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.

| Poskytovatel | Poskytuje konfiguraci z |
| -------- | ----------------------------------- |
| [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault |
| [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration |
| [Zprostředkovatel konfigurace příkazového řádku](#clcp) | Parametry příkazového řádku |
| [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj |
| [Poskytovatel konfigurace proměnných prostředí](#evcp) | Proměnné prostředí |
| [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory INI, JSON a XML |
| [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře |
| [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti |
| [Správce tajných klíčů](xref:security/app-secrets)  | Soubor v adresáři profilu uživatele |

Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou zadáni poskytovatelé konfigurace. Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.

Typická posloupnost zprostředkovatelů konfigurace je:

1. *appsettings.jsna*
1. *appSettings*. `Environment` .. formát *JSON*
1. [Správce tajných klíčů](xref:security/app-secrets)
1. Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#evcp).
1. Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

Běžný postup je přidat poskytovatele konfigurace příkazového řádku na poslední v řadě poskytovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.

Předchozí sekvence zprostředkovatelů se používá ve [výchozí konfiguraci](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Předpony připojovacího řetězce

Rozhraní API pro konfiguraci má speciální pravidla zpracování pro proměnné prostředí pro čtyři připojovací řetězce. Tyto připojovací řetězce jsou součástí konfigurace připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předponami, které jsou uvedené v tabulce, se načtou do aplikace s [výchozí konfigurací](#default) nebo když není dodána žádná předpona `AddEnvironmentVariables` .

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:

* Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).
* Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).

| Klíč proměnné prostředí | Konfigurační klíč převedený na převod | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `System.Data.SqlClient`  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Poskytovatel konfigurace souboru

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načtení konfigurace ze systému souborů. Následující poskytovatelé konfigurace jsou odvozeni z `FileConfigurationProvider` :

* [Poskytovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#jcp)
* [Poskytovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Poskytovatel konfigurace INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.

Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

V předchozím kódu nastavení *MyIniConfig.ini* a  *MyIniConfig*. `Environment` . soubory *ini* jsou přepsány nastavením v:

* [Poskytovatel konfigurace proměnných prostředí](#evcp)
* [Poskytovatel konfigurace příkazového řádku](#clcp)

[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyIniConfig.ini* :

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načte konfiguraci z párů klíč-hodnota souboru JSON.

Přetížení můžou specifikovat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.

Uvažujte následující kód:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Předcházející kód:

* Nakonfiguruje zprostředkovatele konfigurace JSON, aby načetl *MyConfig.js* do souboru s následujícími možnostmi:
  * `optional: true`: Soubor je nepovinný.
  * `reloadOnChange: true` : Soubor se znovu načte při uložení změn.
* Přečte [výchozí zprostředkovatele konfigurace](#default) před *MyConfig.jsv* souboru. Nastavení v nastavení *MyConfig.jspři* přepsání souborů ve výchozích poskytovatelích konfigurace, včetně [poskytovatele konfigurace proměnných prostředí](#evcp) a [poskytovatele konfigurace příkazového řádku](#clcp).

***Obvykle nechcete, aby vlastní*** soubor JSON přepsal hodnoty nastavené ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a v [poskytovateli konfigurace příkazového řádku](#clcp).

Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

V předchozím kódu nastavení *MyConfig.js* v a  *MyConfig*. `Environment` . soubory *JSON* :

* Přepsat nastavení v *appsettings.jsna* a *appSettings*. `Environment` .. soubory *JSON* .
* Jsou přepsány nastavením ve [zprostředkovateli konfigurace proměnných prostředí](#evcp) a [poskytovatelem konfigurace příkazového řádku](#clcp).

[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující *MyConfig.js* souboru:

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Poskytovatel konfigurace XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.

Následující kód vymaže všechny poskytovatele konfigurace a přidá několik poskytovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

V předchozím kódu nastavení *MyXMLFile.xml* a  *MyXMLFile*. `Environment` . soubory *XML* jsou přepsány nastavením v:

* [Poskytovatel konfigurace proměnných prostředí](#evcp)
* [Poskytovatel konfigurace příkazového řádku](#clcp)

[Ukázka ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) obsahuje následující soubor *MyXMLFile.xml* :

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje několik předchozích nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Následující kód přečte předchozí konfigurační soubor a zobrazí klíče a hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Atributy lze použít k zadávání hodnot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Předchozí konfigurační soubor načte následující klíče pomocí `value` :

* Key: – atribut
* oddíl: klíč: atribut

## <a name="key-per-file-configuration-provider"></a>Poskytovatel konfigurace klíče na soubor

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.

Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> . `directoryPath`Do souborů musí být absolutní cesta.

Přetížení umožňují zadat:

* `Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.
* Zda je adresář nepovinný a cesta k adresáři.

Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Poskytovatel konfigurace paměti

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.

Následující kód přidá kolekci paměti do konfiguračního systému:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zobrazuje předchozí nastavení konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

V předchozím kódu `config.AddInMemoryCollection(Dict)` se přidá za [výchozí poskytovatelé konfigurace](#default). Příklad řazení zprostředkovatelů konfigurace najdete v tématu [zprostředkovatel konfigurace JSON](#jcp).

Viz [vazba pole](#boa) pro jiný příklad pomocí `MemoryConfigurationProvider` .

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

Pokud v předchozím kódu `NumberKey` není v konfiguraci nalezeno, `99` je použita výchozí hodnota.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren a Exists

V následujících příkladech zvažte následující *MySubsection.js* souboru:

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Následující kód přidá *MySubsection.js* do zprostředkovatelů konfigurace:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) vrátí dílčí část konfigurace se zadaným klíčem dílčího oddílu.

Následující kód vrátí hodnoty pro `section1` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Následující kód vrátí hodnoty pro `section2:subsection0` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection` nikdy nevrátí `null` . Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.

Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.

### <a name="getchildren-and-exists"></a>GetChildren a existuje

Následující kód volá [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) a vrátí hodnoty pro `section2:subsection0` :

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Předchozí kód volá [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pro ověření, zda existuje oddíl:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Vazba pole

[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Libovolný formát pole, který zveřejňuje numerický klíčový segment, je schopný vytvořit vazbu pole k poli třídy [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .

Zvažte *MyArray.jsna* [webu ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Následující kód přidá *MyArray.js* do zprostředkovatelů konfigurace:

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

V předchozím výstupu má index 3 hodnotu odpovídající hodnotě `value40` `"4": "value40",` v *MyArray.js*. Vazby vázaného pole jsou souvislé a nejsou vázané na index konfiguračního klíče. Pořadač konfigurace nemůže svázat hodnoty null ani vytvářet položky null ve vázaných objektech.

Následující kód načte `array:entries` konfiguraci pomocí <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metody rozšíření:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Následující kód přečte konfiguraci v `arrayDict` `Dictionary` a zobrazí hodnoty:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Předchozí kód vrátí následující výstup:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` . Když jsou data konfigurace obsahující pole svázané, používají se k iteraci konfiguračních dat při vytváření objektu pole indexy v konfiguračních klíčích. Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na instanci doplní `ArrayExample` jakýmkoli poskytovatelem konfigurace, který načte &num; dvojici klíč/hodnota indexu 3. V souboru ukázka stažení zvažte následující *Value3.js* :

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Následující kód obsahuje konfiguraci pro *Value3.jsv* a `arrayDict` `Dictionary` :

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

Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.

## <a name="custom-configuration-provider"></a>Vlastní poskytovatel konfigurace

Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).

Zprostředkovatel má následující vlastnosti:

* Pro demonstrační účely se používá databáze EF v paměti. Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.
* Poskytovatel čte databázovou tabulku do konfigurace při spuštění. Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.
* Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.

Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> . Poskytovatel konfigurace inicializuje databázi, pokud je prázdná. Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Konfigurace přístupu při spuštění

Následující kód zobrazuje konfigurační data v `Startup` metodách:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-no-locrazor-pages"></a>Přístup ke konfiguraci na Razor stránkách

Následující kód zobrazuje konfigurační data na Razor stránce:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

V následujícím kódu `MyOptions` je přidán do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a vázaný na konfiguraci:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Následující kód používá [`@inject`](xref:mvc/views/razor#inject) Razor direktivu k vyřešení a zobrazení hodnot možností:

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Přístup ke konfiguraci v souboru zobrazení MVC

Následující kód zobrazuje konfigurační data v zobrazení MVC:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a>Konfigurace možností s delegátem

Možnosti nakonfigurované v delegátech přepíší hodnoty nastavené ve zprostředkovatelích konfigurace.

Konfigurace možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.

V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je služba přidána do kontejneru služby. Pomocí delegáta konfiguruje hodnoty pro `MyOptions` :

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

Následující kód zobrazí hodnoty možností:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

V předchozím příkladu `Option1` jsou hodnoty a `Option2` zadány v *appsettings.js* a poté přepsány nakonfigurovaným delegátem.

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Konfigurace versus konfigurace aplikace

Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný. Hostitel zodpovídá za spuštění aplikace a správu životního cyklu. Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele. Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Výchozí konfigurace hostitele

Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables). Předpona ( `DOTNET_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Je navázána výchozí konfigurace webového hostitele ( `ConfigureWebHostDefaults` ):
  * Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.
  * Přidejte middleware pro filtrování hostitele.
  * Přidejte middleware předávaných hlaviček, pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` je proměnná prostředí nastavena na `true` .
  * Povolte integraci služby IIS.

## <a name="other-configuration"></a>Další konfigurace

Toto téma se týká pouze *Konfigurace aplikace*. Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:

* *launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:
  * V <xref:fundamentals/environments#development> .
  * V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.
* *web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Proměnné prostředí nastavené v *launchSettings.jspři* přepisování nastavení v prostředí systému.

Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .

## <a name="add-configuration-from-an-external-assembly"></a>Přidat konfiguraci z externího sestavení

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* [Zdrojový kód konfigurace](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*. Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:

* Azure Key Vault
* Azure App Configuration
* Argumenty příkazového řádku
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno)
* Soubory adresáře
* Proměnné prostředí
* Objekty .NET v paměti
* Soubory nastavení

Do [Microsoft. AspNetCore. app metapackageu](xref:fundamentals/metapackage-app)patří konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)).

Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:

```csharp
using Microsoft.Extensions.Configuration;
```

*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu. Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Konfigurace versus konfigurace aplikace

Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný. Hostitel zodpovídá za spuštění aplikace a správu životního cyklu. Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele. Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host> .

## <a name="other-configuration"></a>Další konfigurace

Toto téma se týká pouze *Konfigurace aplikace*. Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:

* *launch.jsna* / *launchSettings.jsv* nástroji jsou konfigurační soubory nástroje pro vývojové prostředí popsané níže:
  * V <xref:fundamentals/environments#development> .
  * V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.
* *web.config* je konfigurační soubor serveru, který je popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations> .

## <a name="default-configuration"></a>Výchozí konfigurace

Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele. `CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host). Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT` ) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona ( `ASPNETCORE_` ) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Konfigurace aplikace se poskytuje:
  * *appsettings.js* používání [poskytovatele konfigurace souboru](#file-configuration-provider).
  * *appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * [Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v `Development` prostředí pomocí položky sestavení
  * Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

## <a name="security"></a>Zabezpečení

Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:

* Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.
* Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.

Další informace najdete v následujících tématech:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Obsahuje rady týkající se použití proměnných prostředí k ukládání citlivých dat. Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému. Poskytovatel konfigurace souboru je popsán dále v tomto tématu.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace. Další informace naleznete v tématu <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hierarchická konfigurační data

Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.

V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:

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

Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace. Oddíly a klíče jsou shrnuty s použitím dvojtečky ( `:` ) pro zachování původní struktury:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*><xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*>metody a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech. Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Konvence

### <a name="sources-and-providers"></a>Zdroje a poskytovatelé

Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.

Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení. Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.

<xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do Razor stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pro získání konfigurace pro třídu.

V následujících příkladech se `_config` pole používá pro přístup k hodnotám konfigurace:

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

Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.

### <a name="keys"></a>Klíče

Konfigurační klíče přijímají následující konvence:

* U klíčů se nerozlišují malá a velká písmena. Například `ConnectionString` a `connectionstring` jsou považovány za ekvivalentní klíče.
* Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči. Další informace o duplicitních klíčích JSON najdete v [tomto problému GitHubu](https://github.com/dotnet/extensions/issues/2381).
* Hierarchické klíče
  * V rozhraní API pro konfiguraci funguje oddělovač dvojtečky ( `:` ) na všech platformách.
  * V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách. Dvojité podtržítko ( `__` ) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.
  * V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač. Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .

### <a name="values"></a>Hodnoty

Konfigurační hodnoty přijímají následující konvence:

* Hodnoty jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.

## <a name="providers"></a>Zprostředkovatelé

V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.

| Poskytovatel | Poskytuje konfiguraci z&hellip; |
| -------- | ----------------------------------- |
| [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* ) | Azure Key Vault |
| [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure) | Azure App Configuration |
| [Zprostředkovatel konfigurace příkazového řádku](#command-line-configuration-provider) | Parametry příkazového řádku |
| [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj |
| [Poskytovatel konfigurace proměnných prostředí](#environment-variables-configuration-provider) | Proměnné prostředí |
| [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory (INI, JSON, XML) |
| [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře |
| [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti |
| [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata) | Soubor v adresáři profilu uživatele |

Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění. Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá. Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.

Typická posloupnost zprostředkovatelů konfigurace je:

1. Soubory (*appsettings.jsna*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)
1. Proměnné prostředí
1. Argumenty příkazového řádku

Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.

Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` . Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurace tvůrce hostitele pomocí UseConfiguration

Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> na tvůrce hostitele s konfigurací.

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

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s automaticky přidanými uživateli `CreateDefaultBuilder` :

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku

Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte jako `AddCommandLine` Poslední:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder

Chcete-li odebrat poskytovatele přidaných nástrojem `CreateDefaultBuilder` , zavolejte nejprve [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Využití konfigurace při spuštění aplikace

Konfigurace dodaná do aplikace v nástroji `ConfigureAppConfiguration` je k dispozici během spouštění aplikace, včetně `Startup.ConfigureServices` . Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .

## <a name="command-line-configuration-provider"></a>Zprostředkovatel konfigurace příkazového řádku

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.

Chcete-li aktivovat konfiguraci příkazového řádku, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> je metoda rozšíření volána na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

`AddCommandLine` je automaticky volána `CreateDefaultBuilder(string [])` , když je volána metoda. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy. Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.

`CreateDefaultBuilder` funguje, když je hostitel vytvořen. Proto může konfigurace z příkazového řádku aktivované nástrojem `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.

Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány pomocí `CreateDefaultBuilder` . Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte na `AddCommandLine` Poslední.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .

1. Otevřete příkazový řádek v adresáři projektu.
1. Zadejte do příkazu argument příkazového řádku `dotnet run` , `dotnet run CommandLineKey=CommandLineValue` .
1. Po spuštění aplikace otevřete v aplikaci prohlížeč `http://localhost:5000` .
1. Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k dispozici pro příkaz `dotnet run` .

### <a name="arguments"></a>Arguments

Hodnota musí následovat po znaménku rovná se ( `=` ), nebo klíč musí obsahovat předponu ( `--` nebo `/` ), pokud se hodnota řídí mezerou. Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=` ).

| Klíčová předpona               | Příklad                                                |
| ------------------------ | ------------------------------------------------------ |
| Bez předpony                | `CommandLineKey1=value1`                               |
| Dvě pomlčky ( `--` )        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Lomítko ( `/` )      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.

Příklady příkazů:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapování přepínačů

Mapování přepínačů povolují logiku nahrazení názvu klíče. Při ručním sestavování konfigurace pomocí nástroje <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> Zadejte slovník přepínačů pro vložení do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.

Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota. Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou ( `-` ).

Pravidla klíče slovníku mapování přepínačů:

* Přepínače musí začínat spojovníkem ( `-` ) nebo dvojitou čárkou ( `--` ).
* Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.

Vytvořte slovník mapování přepínačů. V následujícím příkladu se vytvoří mapování dvou přepínačů:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty. `CreateDefaultBuilder` `AddCommandLine` Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder` . Řešení nebude předávat argumenty, `CreateDefaultBuilder` ale místo toho, aby `ConfigurationBuilder` Metoda metody mohla `AddCommandLine` zpracovat argumenty a slovník mapování přepínačů.

Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.

| Klíč       | Hodnota             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.

| Klíč               | Hodnota    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Poskytovatel konfigurace proměnných prostředí

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.

Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí. Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` slouží k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.
* Volitelná konfigurace z *appsettings.jsna* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* . Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .

Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Zavolejte `AddEnvironmentVariables` jako poslední, pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů.

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables` .

1. Spusťte ukázkovou aplikaci. Otevřete v aplikaci prohlížeč `http://localhost:5000` .
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT` . Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.

Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí. Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.

K vystavení všech proměnných prostředí, které jsou k dispozici pro aplikaci, změňte na `FilteredConfiguration` *stránkách/index. cshtml. cs* následující:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Předpony

Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody. Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_` , zadejte předponu do poskytovatele konfigurace:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.

Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí. Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.

**Předpony připojovacího řetězce**

Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána žádná předpona `AddEnvironmentVariables` .

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:

* Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče ( `ConnectionStrings` ).
* Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_` , který nemá zadaného poskytovatele).

| Klíč proměnné prostředí | Konfigurační klíč převedený na převod | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName` :<br>Hodnota: `System.Data.SqlClient`  |

**Příklad**

Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:

* Název: `CUSTOMCONNSTR_ReleaseDB`
* Hodnota: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Pokud `IConfiguration` je vloženo a přiřazeno k poli s názvem `_config` , přečtěte si hodnotu:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Poskytovatel konfigurace souboru

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načtení konfigurace ze systému souborů. Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:

* [Poskytovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#json-configuration-provider)
* [Poskytovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Poskytovatel konfigurace INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.

Chcete-li aktivovat konfiguraci souboru INI, zavolejte <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value` :

* section0:key0
* section0: klíč1
* Section1: dílčí oddíl: klíč
* section2: subsection0: klíč
* section2: subsection1: klíč

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.

Chcete-li aktivovat konfiguraci souboru JSON, zavolejte <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.

`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele v nástroji `CreateDefaultBuilder` . Metoda je volána pro načtení konfigurace z:

* *appsettings.js*: Tento soubor je nejprve přečten. Verze prostředí souboru může přepsat hodnoty poskytnuté *appsettings.jsv* souboru.
* *appSettings. {Environment}. JSON*: verze prostředí souboru se načte na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Proměnné prostředí.
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Jako první se navázal Poskytovatel konfigurace JSON. Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .

Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appsettings.jsv* a *appSettings. { Prostředí}. JSON*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Příklad**

Ukázková aplikace využívá ke sestavení hostitele výhodu technologie statického usnadnění `CreateDefaultBuilder` , která zahrnuje dvě volání `AddJsonFile` :

* První volání `AddJsonFile` načtení konfigurace z *appsettings.js*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*. Pro *appsettings.Development.js* v ukázkové aplikaci se načte následující soubor:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Spusťte ukázkovou aplikaci. Otevřete v aplikaci prohlížeč `http://localhost:5000` .
1. Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace. Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.
1. Znovu spusťte ukázkovou aplikaci v produkčním prostředí:
   1. Otevřete *vlastnosti/launchSettings.jsv* souboru.
   1. V `ConfigurationSample` profilu změňte hodnotu `ASPNETCORE_ENVIRONMENT` proměnné prostředí na `Production` .
   1. Uložte soubor a spusťte aplikaci `dotnet run` v příkazovém prostředí.
1. Nastavení v *appsettings.Development.js* už nepřepisují nastavení v *appsettings.jsna*. Úroveň protokolu pro klíč `Logging:LogLevel:Default` je `Warning` .

### <a name="xml-configuration-provider"></a>Poskytovatel konfigurace XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.

Chcete-li aktivovat konfiguraci souboru XML, zavolejte <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>Slouží k přístupu k souboru.

Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace. Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value` :

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

Opakující se prvky, které používají stejný název elementu fungují, pokud `name` je atribut použit k rozlišení prvků:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value` :

* oddíl: section0: Key: Key0
* oddíl: section0: Key: klíč1
* oddíl: Section1: Key: Key0
* oddíl: Section1: Key: klíč1

Atributy lze použít k zadávání hodnot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Předchozí konfigurační soubor načte následující klíče pomocí `value` :

* Key: – atribut
* oddíl: klíč: atribut

## <a name="key-per-file-configuration-provider"></a>Poskytovatel konfigurace klíče na soubor

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>Používá soubory adresáře jako konfigurační páry klíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.

Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> . `directoryPath`Do souborů musí být absolutní cesta.

Přetížení umožňují zadat:

* `Action<KeyPerFileConfigurationSource>`Delegát, který konfiguruje zdroj.
* Zda je adresář nepovinný a cesta k adresáři.

Dvojité podtržítko ( `__` ) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System` .

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Poskytovatel konfigurace paměti

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>Používá kolekci v paměti jako konfigurační páry klíč-hodnota.

Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> .

Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>` .

Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.

V následujícím příkladu je vytvořen konfigurační slovník:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Slovník se používá s voláním k `AddInMemoryCollection` poskytnutí konfigurace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce. Přetížení přijímá výchozí hodnotu.

Následující příklad:

* Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey` . Pokud se `NumberKey` v konfiguračních klíčích nenajde, použije se výchozí hodnota `99` .
* Zadá hodnotu jako `int` .
* Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.

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

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren a Exists

V následujících příkladech zvažte následující soubor JSON. Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:

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

Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1
* section2:subsection0:key0
* section2: subsection0: klíč1
* section2:subsection1:key0
* section2: subsection1: klíč1

### <a name="getsection"></a>GetSection

[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.

Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1` , zavolejte `GetSection` a zadejte název oddílu:

```csharp
var configSection = _config.GetSection("section1");
```

`configSection`Hodnota neobsahuje pouze klíč a cestu.

Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0` , zavolejte `GetSection` a zadejte cestu k oddílu:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` nikdy nevrátí `null` . Pokud se nenalezne shodný oddíl, `IConfigurationSection` vrátí se prázdný řetězec.

Při `GetSection` vrácení odpovídajícího oddílu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> není naplněna hodnota. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key>A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.

### <a name="getchildren"></a>GetChildren –

Volání [IConfiguration. Getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Existuje

Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Vzhledem k ukázkovým datům `sectionExists` je `false` to proto, že `section2:subsection2` v konfiguračních datech není oddíl.

## <a name="bind-to-an-object-graph"></a>Vytvoření vazby na graf objektů

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO. Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.

Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Ukázková aplikace má *tvshow.xml* soubor obsahující konfigurační data:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Konfigurace je svázána s celým `TvShow` grafem objektů pomocí `Bind` metody. Vázaná instance je přiřazena k vlastnosti pro vykreslování:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ. `Get<T>` je pohodlnější než použití `Bind` . Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

*Ukázková aplikace ukazuje koncepty popsané v této části.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Libovolný formát pole, který zveřejňuje numerický klíčový segment ( `:0:` , `:1:` ,), &hellip; `:{n}:` je schopný vytvořit vazbu pole k poli třídy POCO.

> [!NOTE]
> Vazba je poskytována podle konvence. Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.

**Zpracování pole v paměti**

Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.

| Klíč             | Hodnota  |
| :-------------: | :----: |
| pole: položky: 0 | value0 |
| pole: položky: 1 | Hodnota1 |
| pole: položky: 2 | Argument |
| pole: položky: 4 | value4 |
| pole: položky: 5 | value5 |

Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Pole přeskočí hodnotu indexu &num; 3. Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.

V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Konfigurační data jsou svázána s objektem:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lze také použít syntaxi, což má za následek kompaktnější kód:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Vázaný objekt, instance `ArrayExample` , přijímá data pole z konfigurace.

| `ArrayExample.Entries` Indexovacím | `ArrayExample.Entries` Osa |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num; 3 ve vázaném objektu obsahuje konfigurační data pro `array:4` konfigurační klíč a jeho hodnotu `value4` . Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu. Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace pro index &num; 3 se dá zadat předtím, než se vazba na `ArrayExample` instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci. Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:

*missing_value.js*:

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

Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.

| Klíč             | Hodnota  |
| :-------------: | :----: |
| pole: položky: 3 | hodnota3 |

Je-li `ArrayExample` instance třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index &num; 3, `ArrayExample.Entries` pole obsahuje hodnotu.

| `ArrayExample.Entries` Indexovacím | `ArrayExample.Entries` Osa |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | hodnota3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Zpracování pole JSON**

Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule. V následujícím konfiguračním souboru `subsection` je pole:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:

| Klíč                     | Hodnota  |
| ----------------------- | :----: |
| json_array: klíč          | Hodnotaa |
| json_array: pododdíl: 0 | Hodnotab |
| json_array: pododdíl: 1 | valueC |
| json_array: pododdíl: 2 | s |

V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu `valueA` . Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection` .

| `JsonArrayExample.Subsection` Indexovacím | `JsonArrayExample.Subsection` Osa |
| :---------------------------------: | :---------------------------------: |
| 0                                   | Hodnotab                              |
| 1                                   | valueC                              |
| 2                                   | s                              |

## <a name="custom-configuration-provider"></a>Vlastní poskytovatel konfigurace

Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).

Zprostředkovatel má následující vlastnosti:

* Pro demonstrační účely se používá databáze EF v paměti. Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.
* Poskytovatel čte databázovou tabulku do konfigurace při spuštění. Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.
* Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.

Definujte `EFConfigurationValue` entitu pro ukládání konfiguračních hodnot do databáze.

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` do úložiště a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource> .

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> . Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration`Metoda rozšíření umožňuje přidat zdroj konfigurace do `ConfigurationBuilder` .

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Konfigurace přístupu během spuštění

Vložení `IConfiguration` do `Startup` konstruktoru pro přístup k hodnotám konfigurace v `Startup.ConfigureServices` . Chcete-li získat přístup ke konfiguraci v `Startup.Configure` , buď `IConfiguration` přímo do metody, nebo použijte instanci z konstruktoru:

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

Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a>Přístup ke konfiguraci na Razor stránce stránky nebo zobrazení MVC

Chcete-li získat přístup k nastavení konfigurace na Razor stránce stránky nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([Referenční dokumentace jazyka C#: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [ obor názvůMicrosoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) a vložte ho <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.

Na Razor stránce stránky:

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

## <a name="add-configuration-from-an-external-assembly"></a>Přidat konfiguraci z externího sestavení

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/configuration/options>

::: moniker-end
