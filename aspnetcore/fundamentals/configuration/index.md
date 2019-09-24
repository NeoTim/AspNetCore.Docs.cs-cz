---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 357a3d89648086f0329cd16bc9d72863df9bdcd6
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71217783"
---
# <a name="configuration-in-aspnet-core"></a>Konfigurace v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*. Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:

* Azure Key Vault
* Konfigurace aplikace Azure
* Argumenty příkazového řádku
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno)
* Soubory adresáře
* Proměnné prostředí
* Objekty .NET v paměti
* Soubory nastavení

::: moniker range=">= aspnetcore-3.0"

Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

::: moniker-end

Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:

```csharp
using Microsoft.Extensions.Configuration;
```

*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu. Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Konfigurace versus konfigurace aplikace

Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele. Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele <xref:fundamentals/index#host>, najdete v tématu.

## <a name="default-configuration"></a>Výchozí konfigurace

::: moniker range=">= aspnetcore-3.0"

Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele. `CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host). Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s `DOTNET_` předponou ( `DOTNET_ENVIRONMENT`například) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Je navázána výchozí konfigurace webového`ConfigureWebHostDefaults`hostitele ():
  * Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.
  * Přidejte middleware pro filtrování hostitele.
  * Přidejte middleware předávaných hlaviček `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je proměnná prostředí `true`nastavena na.
  * Povolte integraci služby IIS.
* Konfigurace aplikace se poskytuje:
  * *appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * *appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * [Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení
  * Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele. `CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host). Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s `ASPNETCORE_` předponou ( `ASPNETCORE_ENVIRONMENT`například) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Konfigurace aplikace se poskytuje:
  * *appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * *appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * [Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení
  * Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

::: moniker-end

## <a name="security"></a>Zabezpečení

Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:

* Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.
* Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.

Další informace naleznete v následujících tématech:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>&ndash; Obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat. Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému. Poskytovatel konfigurace souboru je popsán dále v tomto tématu.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace. Další informace naleznete v tématu <xref:security/key-vault-configuration>.

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

Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace. Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) pro zachování původní struktury:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech. Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Konvence

### <a name="sources-and-providers"></a>Zdroje a poskytovatelé

Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.

Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení. Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.

<xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> a získat tak konfiguraci pro třídu:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.

### <a name="keys"></a>Klíče

Konfigurační klíče přijímají následující konvence:

* U klíčů se nerozlišují malá a velká písmena. Například `ConnectionString` a`connectionstring` jsou považovány za ekvivalentní klíče.
* Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.
* Hierarchické klíče
  * V rozhraní API pro konfiguraci funguje oddělovač dvojtečky`:`() na všech platformách.
  * V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách. Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.
  * V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač. Pokud jsou tajné klíče načteny do konfigurace aplikace, je nutné zadat kód, který nahradí pomlčky dvojtečkou.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .

### <a name="values"></a>Hodnoty

Konfigurační hodnoty přijímají následující konvence:

* Hodnoty jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.

## <a name="providers"></a>dodavateli

V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.

| Poskytovatel | Poskytuje konfiguraci z&hellip; |
| -------- | ----------------------------------- |
| [Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (Témata*zabezpečení* ) | Azure Key Vault |
| [Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Dokumentace k Azure) | Konfigurace aplikace Azure |
| [Zprostředkovatel konfigurace příkazového řádku](#command-line-configuration-provider) | Parametry příkazového řádku |
| [Vlastní poskytovatel konfigurace](#custom-configuration-provider) | Vlastní zdroj |
| [Poskytovatel konfigurace proměnných prostředí](#environment-variables-configuration-provider) | Proměnné prostředí |
| [Poskytovatel konfigurace souboru](#file-configuration-provider) | Soubory (INI, JSON, XML) |
| [Poskytovatel konfigurace klíče na soubor](#key-per-file-configuration-provider) | Soubory adresáře |
| [Poskytovatel konfigurace paměti](#memory-configuration-provider) | Kolekce v paměti |
| [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (Témata*zabezpečení* ) | Soubor v adresáři profilu uživatele |

Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění. Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, ve kterém je váš kód může uspořádat. Seřazení zprostředkovatelů konfigurace v kódu tak, aby vyhovoval vašim prioritám pro základní zdroje konfigurace.

Typická posloupnost zprostředkovatelů konfigurace je:

1. Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (Jenom vývojové prostředí)
1. Proměnné prostředí
1. Argumenty příkazového řádku

Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.

Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v `CreateDefaultBuilder`nástroji. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a>Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration

Chcete-li nakonfigurovat tvůrce hostitele, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> zavolejte a zadejte konfiguraci. `ConfigureHostConfiguration`slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení. `ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurace tvůrce hostitele pomocí UseConfiguration

Chcete-li nakonfigurovat tvůrce hostitele, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zavolejte na tvůrce hostitele s konfigurací.

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

::: moniker-end

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s `CreateDefaultBuilder`automaticky přidanými uživateli:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a>Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku

Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku `AddCommandLine` , zavolejte jako poslední:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a>Využití konfigurace při spuštění aplikace

Konfigurace dodaná do aplikace v `ConfigureAppConfiguration` nástroji je k dispozici během spouštění aplikace, `Startup.ConfigureServices`včetně. Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .

## <a name="command-line-configuration-provider"></a>Zprostředkovatel konfigurace příkazového řádku

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.

Chcete-li aktivovat konfiguraci příkazového řádku <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> , je metoda rozšíření volána na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

`AddCommandLine`je automaticky volána, `CreateDefaultBuilder(string [])` když je volána metoda. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder`také načte:

* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy. Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.

`CreateDefaultBuilder`funguje, když je hostitel vytvořen. Proto může konfigurace z příkazového řádku aktivované `CreateDefaultBuilder` nástrojem ovlivnit způsob konfigurace hostitele.

Pro aplikace založené na šablonách `AddCommandLine` ASP.NET Core již byly volány pomocí. `CreateDefaultBuilder` Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` na poslední.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Příklad**

Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>volání.

1. Otevřete příkazový řádek v adresáři projektu.
1. Zadejte do `dotnet run` příkazu argument příkazového řádku, `dotnet run CommandLineKey=CommandLineValue`.
1. Po spuštění aplikace otevřete v `http://localhost:5000`aplikaci prohlížeč.
1. Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k `dotnet run`dispozici pro příkaz.

### <a name="arguments"></a>Arguments

Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou. Hodnota není povinná, `CommandLineKey=`Pokud se používá znaménko rovná se (například).

| Klíčová předpona               | Příklad                                                |
| ------------------------ | ------------------------------------------------------ |
| Bez předpony                | `CommandLineKey1=value1`                               |
| Dvě pomlčky (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Lomítko (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.

Příklady příkazů:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapování přepínačů

Mapování přepínačů povolují logiku nahrazení názvu klíče. Při ručním sestavení konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>nástroje můžete zadat slovník přepínačů <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> pro vložení do metody.

Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota. Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou (`-`).

Pravidla klíče slovníku mapování přepínačů:

* Přepínače musí začínat spojovníkem`-`() nebo dvojitou čárkou (`--`).
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

Pro aplikace, které používají mapování přepínačů, by `CreateDefaultBuilder` volání nemělo předávat argumenty. Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder`. `CreateDefaultBuilder` `AddCommandLine` Řešení nebude předávat argumenty `CreateDefaultBuilder` , ale místo toho `ConfigurationBuilder` , aby `AddCommandLine` metoda metody mohla zpracovat argumenty a slovník mapování přepínačů.

Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.

| Key       | Value             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.

| Key               | Value    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Poskytovatel konfigurace proměnných prostředí

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.

Chcete-li aktivovat konfiguraci proměnných prostředí, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí. Další informace najdete v tématu [aplikace Azure: Přepište konfiguraci aplikace pomocí webu Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)Portal.

::: moniker range=">= aspnetcore-3.0"

`AddEnvironmentVariables`slouží k načtení proměnných prostředí `DOTNET_` s předponou pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem s [obecným hostitelem](xref:fundamentals/host/generic-host) a `CreateDefaultBuilder` který je volán. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`AddEnvironmentVariables`slouží k načtení proměnných prostředí `ASPNETCORE_` s předponou pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

::: moniker-end

`CreateDefaultBuilder`také načte:

* Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.
* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* . Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .

Pokud potřebujete zadat konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

**Příklad**

Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje `AddEnvironmentVariables`volání.

1. Spuštění ukázkové aplikace. Otevřete v `http://localhost:5000`aplikaci prohlížeč.
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou `ENVIRONMENT`prostředí. Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.

Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí. Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.

Pokud chcete zpřístupnit všechny proměnné prostředí, které jsou k dispozici pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* následující:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Předpony

Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody. Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_`, zadejte předponu do poskytovatele konfigurace:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.

Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí. Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.

**Předpony připojovacího řetězce**

Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána `AddEnvironmentVariables`žádná předpona.

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:

* Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče (`ConnectionStrings`).
* Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).

| Klíč proměnné prostředí | Konfigurační klíč převedený na převod | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | Položka konfigurace není vytvořená.                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | Klíč: `ConnectionStrings:<KEY>_ProviderName`:<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | Klíč: `ConnectionStrings:<KEY>_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | Klíč: `ConnectionStrings:<KEY>_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |

## <a name="file-configuration-provider"></a>Poskytovatel konfigurace souboru

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů. Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:

* [Poskytovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#json-configuration-provider)
* [Poskytovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Poskytovatel konfigurace INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.

Chcete-li aktivovat konfiguraci souboru INI, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* Section1: dílčí oddíl: klíč
* section2: subsection0: klíč
* section2: subsection1: klíč

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.

Chcete-li aktivovat konfiguraci souboru JSON, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.

`AddJsonFile`je automaticky volána dvakrát při inicializaci nového tvůrce hostitele pomocí `CreateDefaultBuilder`nástroje. Metoda je volána pro načtení konfigurace z:

* *appSettings. JSON* &ndash; tento soubor je nejdřív načtený. Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .
* *appSettings. {Environment}. JSON* &ndash; verze souboru je načtená na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder`také načte:

* Proměnné prostředí.
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Jako první se navázal Poskytovatel konfigurace JSON. Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .

Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.

**Příklad**

Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje dvě `AddJsonFile`volání. Konfigurace je načtena z souboru *appSettings. JSON* a *appSettings. { Environment}. JSON*.

1. Spuštění ukázkové aplikace. Otevřete v `http://localhost:5000`aplikaci prohlížeč.
1. Všimněte si, že výstup obsahuje páry klíč-hodnota pro konfiguraci uvedenou v tabulce v závislosti na prostředí. Klíče konfigurace protokolování používají jako hierarchický oddělovač`:`dvojtečku ().

| Key                        | Hodnota vývoje | Produkční hodnota |
| -------------------------- | :---------------: | :--------------: |
| Protokolování: LogLevel: systém    | Informace o       | Informace o      |
| Logging:LogLevel:Microsoft | Informace o       | Informace o      |
| Protokolování: LogLevel: výchozí   | Ladit             | Chyba            |
| AllowedHosts               | *                 | *                |

### <a name="xml-configuration-provider"></a>Poskytovatel konfigurace XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.

Chcete-li aktivovat konfiguraci souboru XML, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.

Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace. Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

Opakující se prvky, které používají stejný název elementu fungují, `name` Pokud je atribut použit k rozlišení prvků:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* Key: – atribut
* oddíl: klíč: atribut

## <a name="key-per-file-configuration-provider"></a>Poskytovatel konfigurace klíče na soubor

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá soubory adresáře jako konfigurační páry klíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.

Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci. `directoryPath` Do souborů musí být absolutní cesta.

Přetížení umožňují zadat:

* `Action<KeyPerFileConfigurationSource>` Delegát, který konfiguruje zdroj.
* Zda je adresář nepovinný a cesta k adresáři.

Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.

Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.

## <a name="memory-configuration-provider"></a>Poskytovatel konfigurace paměti

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako konfigurační páry klíč-hodnota.

Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.

Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.

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

Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder. GetValue\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ. Přetížení umožňuje zadat výchozí hodnotu, pokud se klíč nenajde.

Následující příklad:

* Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey`. Pokud `NumberKey` se v konfiguračních klíčích nenajde, použije se výchozí `99` hodnota.
* Zadá hodnotu jako `int`.
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

Chcete-li <xref:Microsoft.Extensions.Configuration.IConfigurationSection> vrátit obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:

```csharp
var configSection = _config.GetSection("section1");
```

Hodnota `configSection` neobsahuje pouze klíč a cestu.

Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`nikdy nevrátí `null`. Pokud se nenalezne shodný oddíl, vrátí `IConfigurationSection` se prázdný řetězec.

Při `GetSection` vrácení odpovídajícího <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddílu není naplněna hodnota. <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> A<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.

### <a name="getchildren"></a>GetChildren –

Volání [IConfiguration. getchildrens](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:

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

Vzhledem k ukázkovým datům `sectionExists` je `false` to `section2:subsection2` proto, že v konfiguračních datech není oddíl.

## <a name="bind-to-a-class"></a>Vazba na třídu

Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

Konfigurační hodnoty jsou vraceny jako řetězce, ale <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> volání umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .

Ukázková aplikace obsahuje `Starship` model (*modely/Starship. cs*):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

V `starship` části souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace k načtení konfigurace používá poskytovatele konfigurace JSON:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

Vytvoří se následující páry klíč-hodnota konfigurace:

| Key                   | Value                                             |
| --------------------- | ------------------------------------------------- |
| Starship: název         | USS Enterprise                                    |
| Starship: Registry     | NCC-1701                                          |
| Starship: třída        | Založen                                      |
| Starship: délka       | 304,8                                             |
| Starship: vyřazení z provozu | False                                             |
| Patka             | Paramount Pictures Corp. https://www.paramount.com |

Ukázková aplikace volá `GetSection` `starship` klíč. Páry `starship` klíč-hodnota jsou izolované. Metoda je volána pro dílčí oddíl procházející v instanci `Starship` třídy. `Bind` Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a>Vytvoření vazby na graf objektů

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.

Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

Konfigurace je svázána `TvShow` `Bind` s celým grafem objektů pomocí metody. Vázaná instance je přiřazena k vlastnosti pro vykreslování:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder. Get\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí zadaný typ. `Get<T>`je pohodlnější než použití `Bind`. Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

*Ukázková aplikace ukazuje koncepty popsané v této části.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Libovolný formát pole, který zveřejňuje numerický klíčový segment`:0:`( `:1:`, &hellip; , `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.

> [!NOTE]
> Vazba je poskytována podle konvence. Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.

**Zpracování pole v paměti**

Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.

| Key             | Value  |
| :-------------: | :----: |
| pole: položky: 0 | value0 |
| pole: položky: 1 | Hodnota1 |
| pole: položky: 2 | Argument |
| pole: položky: 4 | value4 |
| pole: položky: 5 | value5 |

Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

Pole přeskočí hodnotu indexu &num;3. Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.

V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

Konfigurační data jsou svázána s objektem:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

Lze také použít syntaxi [ConfigurationBinder. Get\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) , což má za následek kompaktnější kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.

| `ArrayExample.Entries`Indexovacím | `ArrayExample.Entries`Osa |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num;3 ve vázaném objektu obsahuje konfigurační data `array:4` pro konfigurační `value4`klíč a jeho hodnotu. Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu. Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace pro index &num;3 se dá zadat předtím, než se vazba `ArrayExample` na instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci. Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

V `ConfigureAppConfiguration`nástroji:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.

| Key             | Value  |
| :-------------: | :----: |
| pole: položky: 3 | hodnota3 |

Je-li instance &num; `ArrayExample.Entries` třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index 3, pole obsahuje hodnotu. `ArrayExample`

| `ArrayExample.Entries`Indexovacím | `ArrayExample.Entries`Osa |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | hodnota3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Zpracování pole JSON**

Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule. V následujícím konfiguračním souboru `subsection` je pole:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:

| Key                     | Value  |
| ----------------------- | :----: |
| json_array:key          | Hodnotaa |
| json_array: dílčí oddíl: 0 | Hodnotab |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | s |

V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu. `valueA` Hodnoty pododdílu jsou uloženy ve vlastnosti `Subsection`pole POCO.

| `JsonArrayExample.Subsection`Indexovacím | `JsonArrayExample.Subsection`Osa |
| :---------------------------------: | :---------------------------------: |
| 0                                   | Hodnotab                              |
| 1                                   | valueC                              |
| 2                                   | s                              |

## <a name="custom-configuration-provider"></a>Vlastní poskytovatel konfigurace

Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).

Zprostředkovatel má následující vlastnosti:

* Pro demonstrační účely se používá databáze EF v paměti. Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte `ConfigurationBuilder` sekundární pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.
* Poskytovatel čte databázovou tabulku do konfigurace při spuštění. Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.
* Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.

`EFConfigurationValue` Definujte entitu pro ukládání konfiguračních hodnot do databáze.

::: moniker range=">= aspnetcore-3.0"

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

`EFConfigurationContext` Přidejte do úložiště a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda rozšíření umožňuje přidat zdroj konfigurace `ConfigurationBuilder`do. `AddEFConfiguration`

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

`EFConfigurationContext` Přidejte do úložiště a získejte přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda rozšíření umožňuje přidat zdroj konfigurace `ConfigurationBuilder`do. `AddEFConfiguration`

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a>Konfigurace přístupu během spuštění

Vložení `IConfiguration` `Startup.ConfigureServices`do konstruktorupropřístupkhodnotámkonfiguracev.`Startup` Chcete-li získat `Startup.Configure`přístup ke konfiguraci `IConfiguration` v, buď přímo do metody, nebo použijte instanci z konstruktoru:

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

Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete [v tématu spuštění aplikace: Praktické metody](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC

Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.

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

## <a name="add-configuration-from-an-external-assembly"></a>Přidat konfiguraci z externího sestavení

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/options>
