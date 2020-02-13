---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171902"
---
# <a name="configuration-in-aspnet-core"></a>Konfigurace v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*. Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:

* Azure Key Vault
* Azure App Configuration
* Argumenty příkazového řádku
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno)
* Soubory adresáře
* Proměnné prostředí
* Objekty .NET v paměti
* Soubory nastavení

Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.

Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu. Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Konfigurace versus konfigurace aplikace

Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele. Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Další konfigurace

Toto téma se týká pouze *Konfigurace aplikace*. Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:

* *Launch. json*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:
  * V <xref:fundamentals/environments#development>.
  * V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.
* *Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Výchozí konfigurace

Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele. `CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host). Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):
  * Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.
  * Přidejte middleware pro filtrování hostitele.
  * Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.
  * Povolte integraci služby IIS.
* Konfigurace aplikace se poskytuje:
  * *appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * *appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * [Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu
  * Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

## <a name="security"></a>Zabezpečení

Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:

* Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.
* Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.

Další informace najdete v následujících tématech:

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat. Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému. Poskytovatel konfigurace souboru je popsán dále v tomto tématu.

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

Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace. Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech. Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Zásady

### <a name="sources-and-providers"></a>Zdroje a poskytovatelé

Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.

Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení. Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.

<xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.

V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:

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

* U klíčů se nerozlišují malá a velká písmena. Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.
* Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.
* Hierarchické klíče
  * V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.
  * V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách. Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.
  * V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač. Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .

### <a name="values"></a>Hodnoty

Konfigurační hodnoty přijímají následující konvence:

* Hodnoty jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.

## <a name="providers"></a>Poskytovatelé

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

1. Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)
1. Proměnné prostředí
1. Argumenty příkazového řádku

Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.

Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a>Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration

Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci. `ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení. `ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.

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

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku

Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder

Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Využití konfigurace při spuštění aplikace

Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`. Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .

## <a name="command-line-configuration-provider"></a>Zprostředkovatel konfigurace příkazového řádku

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.

Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy. Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.

`CreateDefaultBuilder` funguje při sestavení hostitele. Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.

Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`. Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Otevřete příkazový řádek v adresáři projektu.
1. Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.
1. Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.

### <a name="arguments"></a>Argumenty

Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou. Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).

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

Mapování přepínačů povolují logiku nahrazení názvu klíče. Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.

Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota. Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).

Pravidla klíče slovníku mapování přepínačů:

* Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).
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

Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty. Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`. Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.

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

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.

Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí. Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.
* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* . Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .

Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.

1. Spuštění ukázkové aplikace. Otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`. Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.

Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí. Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.

Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Předpony

Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`. Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.

Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí. Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.

**Předpony připojovacího řetězce**

Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:

* Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).
* Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).

| Klíč proměnné prostředí | Konfigurační klíč převedený na převod | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |

**Příklad**

Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:

* Název &ndash; `CUSTOMCONNSTR_ReleaseDB`
* Hodnota &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Pokud je `IConfiguration` vložená a přiřazená k poli s názvem `_config`, přečtěte si hodnotu:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Poskytovatel konfigurace souboru

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů. Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:

* [Poskytovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#json-configuration-provider)
* [Poskytovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Poskytovatel konfigurace INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.

Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* Section1: dílčí oddíl: klíč
* section2: subsection0: klíč
* section2: subsection1: klíč

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.

Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`. Metoda je volána pro načtení konfigurace z:

* *appSettings. json* &ndash; tento soubor je nejdřív načtený. Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .
* *appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Proměnné prostředí.
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Jako první se navázal Poskytovatel konfigurace JSON. Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .

Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:

* První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*. Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. Spuštění ukázkové aplikace. Otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace. Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.
1. Znovu spusťte ukázkovou aplikaci v produkčním prostředí:
   1. Otevřete soubor *Properties/launchSettings. JSON* .
   1. V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.
   1. Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.
1. Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*. Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Information`.

### <a name="xml-configuration-provider"></a>Poskytovatel konfigurace XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.

Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace. Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:

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

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.

Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. `directoryPath` souborů musí být absolutní cesta.

Přetížení umožňují zadat:

* Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.
* Zda je adresář nepovinný a cesta k adresáři.

Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Poskytovatel konfigurace paměti

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.

Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.

Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.

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

[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce. Přetížení přijímá výchozí hodnotu.

Následující příklad:

* Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`. Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.
* Zadá hodnotu jako `int`.
* Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.

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

Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` nemá hodnotu, jenom klíč a cestu.

Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` nikdy nevrátí `null`. Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.

Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá. Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.

### <a name="getchildren"></a>GetChildren –

Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:

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

Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.

## <a name="bind-to-a-class"></a>Vazba na třídu

Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) . Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu. Pole nejsou **svázána** .

Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

Vytvoří se následující páry klíč-hodnota konfigurace:

| Klíč                   | Hodnota                                             |
| --------------------- | ------------------------------------------------- |
| Starship: název         | USS Enterprise                                    |
| Starship: Registry     | NCC-1701                                          |
| Starship: třída        | Založen                                      |
| Starship: délka       | 304,8                                             |
| Starship: vyřazení z provozu | False                                             |
| Patka             | Paramount Pictures Corp. https://www.paramount.com |

Ukázková aplikace volá `GetSection` s klíčem `starship`. Páry klíč-hodnota `starship` jsou izolované. Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy. Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>Vytvoření vazby na graf objektů

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO. Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.

Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`. Vázaná instance je přiřazena k vlastnosti pro vykreslování:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ. `Get<T>` je pohodlnější než použití `Bind`. Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

*Ukázková aplikace ukazuje koncepty popsané v této části.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.

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

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Pole přeskočí hodnotu pro index &num;3. Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.

V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Konfigurační data jsou svázána s objektem:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.

| Index `ArrayExample.Entries` | Hodnota `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`. Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu. Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci. Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:

*missing_value. JSON*:

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

Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.

| Index `ArrayExample.Entries` | Hodnota `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | hodnota3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Zpracování pole JSON**

Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule. V následujícím konfiguračním souboru je `subsection` pole:

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:

| Klíč                     | Hodnota  |
| ----------------------- | :----: |
| json_array:key          | Hodnotaa |
| json_array: pododdíl: 0 | Hodnotab |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | s |

V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`. Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.

| Index `JsonArrayExample.Subsection` | Hodnota `JsonArrayExample.Subsection` |
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

Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Poskytovatel konfigurace inicializuje databázi, pokud je prázdná. Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Konfigurace přístupu během spuštění

Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`. Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:

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

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC

Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.

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

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/options>

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

Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:

```csharp
using Microsoft.Extensions.Configuration;
```

*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu. Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Konfigurace versus konfigurace aplikace

Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu. V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele. Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Další konfigurace

Toto téma se týká pouze *Konfigurace aplikace*. Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:

* *Launch. json*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:
  * V <xref:fundamentals/environments#development>.
  * V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.
* *Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Výchozí konfigurace

Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele. `CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:

Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host). Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).

* Konfigurace hostitele se poskytuje:
  * Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider). Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).
* Konfigurace aplikace se poskytuje:
  * *appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * *appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).
  * [Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu
  * Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).
  * Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).

## <a name="security"></a>Zabezpečení

Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:

* Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.
* Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.
* Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.

Další informace najdete v následujících tématech:

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat. Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému. Poskytovatel konfigurace souboru je popsán dále v tomto tématu.

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

Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace. Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech. Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Zásady

### <a name="sources-and-providers"></a>Zdroje a poskytovatelé

Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.

Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení. Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.

<xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.

V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:

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

* U klíčů se nerozlišují malá a velká písmena. Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.
* Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.
* Hierarchické klíče
  * V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.
  * V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách. Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.
  * V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač. Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .

### <a name="values"></a>Hodnoty

Konfigurační hodnoty přijímají následující konvence:

* Hodnoty jsou řetězce.
* Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.

## <a name="providers"></a>Poskytovatelé

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

1. Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)
1. Proměnné prostředí
1. Argumenty příkazového řádku

Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.

Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurace tvůrce hostitele pomocí UseConfiguration

Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.

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

Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku

Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder

Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Využití konfigurace při spuštění aplikace

Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`. Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .

## <a name="command-line-configuration-provider"></a>Zprostředkovatel konfigurace příkazového řádku

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.

Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Proměnné prostředí.

`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy. Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.

`CreateDefaultBuilder` funguje při sestavení hostitele. Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.

Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`. Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.

1. Otevřete příkazový řádek v adresáři projektu.
1. Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.
1. Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.

### <a name="arguments"></a>Argumenty

Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou. Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).

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

Mapování přepínačů povolují logiku nahrazení názvu klíče. Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.

Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku. Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota. Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).

Pravidla klíče slovníku mapování přepínačů:

* Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).
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

Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty. Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`. Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.

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

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.

Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí. Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`. Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.
* Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* . Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .

Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.

1. Spuštění ukázkové aplikace. Otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`. Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.

Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí. Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.

Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Předpony

Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`. Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.

Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí. Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.

**Předpony připojovacího řetězce**

Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace. Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.

| Předpona připojovacího řetězce | Poskytovatel |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Vlastní zprostředkovatel |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:

* Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).
* Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).

| Klíč proměnné prostředí | Konfigurační klíč převedený na převod | Položka konfigurace zprostředkovatele                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Položka konfigurace není vytvořená.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klíč: `ConnectionStrings:{KEY}_ProviderName`:<br>Hodnota: `System.Data.SqlClient`  |

**Příklad**

Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:

* Název &ndash; `CUSTOMCONNSTR_ReleaseDB`
* Hodnota &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Pokud je `IConfiguration` vložená a přiřazená k poli s názvem `_config`, přečtěte si hodnotu:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Poskytovatel konfigurace souboru

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů. Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:

* [Poskytovatel konfigurace INI](#ini-configuration-provider)
* [Zprostředkovatel konfigurace JSON](#json-configuration-provider)
* [Poskytovatel konfigurace XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Poskytovatel konfigurace INI

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.

Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* Section1: dílčí oddíl: klíč
* section2: subsection0: klíč
* section2: subsection1: klíč

### <a name="json-configuration-provider"></a>Zprostředkovatel konfigurace JSON

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.

Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`. Metoda je volána pro načtení konfigurace z:

* *appSettings. json* &ndash; tento soubor je nejdřív načtený. Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .
* *appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.

`CreateDefaultBuilder` také načte:

* Proměnné prostředí.
* [Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.
* Argumenty příkazového řádku.

Jako první se navázal Poskytovatel konfigurace JSON. Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .

Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Příklad**

Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:

* První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*. Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Spuštění ukázkové aplikace. Otevřete v aplikaci prohlížeč na `http://localhost:5000`.
1. Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace. Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.
1. Znovu spusťte ukázkovou aplikaci v produkčním prostředí:
   1. Otevřete soubor *Properties/launchSettings. JSON* .
   1. V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.
   1. Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.
1. Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*. Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Warning`.

### <a name="xml-configuration-provider"></a>Poskytovatel konfigurace XML

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.

Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Přetížení umožňují zadat:

* Zda je soubor nepovinný.
* Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.
* <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.

Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace. Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

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

Předchozí konfigurační soubor načte následující klíče pomocí `value`:

* section0:key0
* section0: klíč1
* section1:key0
* Section1: klíč1

Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:

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

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota. Klíč je název souboru. Hodnota obsahuje obsah souboru. Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.

Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>. `directoryPath` souborů musí být absolutní cesta.

Přetížení umožňují zadat:

* Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.
* Zda je adresář nepovinný a cesta k adresáři.

Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů. Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.

Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Poskytovatel konfigurace paměti

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.

Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.

Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.

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

[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce. Přetížení přijímá výchozí hodnotu.

Následující příklad:

* Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`. Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.
* Zadá hodnotu jako `int`.
* Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.

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

Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` nemá hodnotu, jenom klíč a cestu.

Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` nikdy nevrátí `null`. Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.

Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá. Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.

### <a name="getchildren"></a>GetChildren –

Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:

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

Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.

## <a name="bind-to-a-class"></a>Vazba na třídu

Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*. Další informace naleznete v tématu <xref:fundamentals/configuration/options>.

Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) . Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu. Pole nejsou **svázána** .

Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

Vytvoří se následující páry klíč-hodnota konfigurace:

| Klíč                   | Hodnota                                             |
| --------------------- | ------------------------------------------------- |
| Starship: název         | USS Enterprise                                    |
| Starship: Registry     | NCC-1701                                          |
| Starship: třída        | Založen                                      |
| Starship: délka       | 304,8                                             |
| Starship: vyřazení z provozu | False                                             |
| Patka             | Paramount Pictures Corp. https://www.paramount.com |

Ukázková aplikace volá `GetSection` s klíčem `starship`. Páry klíč-hodnota `starship` jsou izolované. Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy. Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>Vytvoření vazby na graf objektů

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO. Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.

Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`. Vázaná instance je přiřazena k vlastnosti pro vykreslování:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ. `Get<T>` je pohodlnější než použití `Bind`. Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Vazba pole na třídu

*Ukázková aplikace ukazuje koncepty popsané v této části.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích. Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.

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

Pole přeskočí hodnotu pro index &num;3. Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.

V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Konfigurační data jsou svázána s objektem:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.

| Index `ArrayExample.Entries` | Hodnota `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`. Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu. Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.

Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci. Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:

*missing_value. JSON*:

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

Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.

| Index `ArrayExample.Entries` | Hodnota `ArrayExample.Entries` |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | Hodnota1                       |
| 2                            | Argument                       |
| 3                            | hodnota3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**Zpracování pole JSON**

Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule. V následujícím konfiguračním souboru je `subsection` pole:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:

| Klíč                     | Hodnota  |
| ----------------------- | :----: |
| json_array:key          | Hodnotaa |
| json_array: pododdíl: 0 | Hodnotab |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | s |

V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`. Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.

| Index `JsonArrayExample.Subsection` | Hodnota `JsonArrayExample.Subsection` |
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

Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.

*Modely/EFConfigurationValue. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.

*EFConfigurationProvider/EFConfigurationContext. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.

*EFConfigurationProvider/EFConfigurationSource. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.

*EFConfigurationProvider/EFConfigurationProvider. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.

*Rozšíření/EntityFrameworkExtensions. cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Konfigurace přístupu během spuštění

Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`. Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:

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

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC

Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.

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

Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/options>

::: moniker-end
