---
title: Soubor web.config
author: rick-anderson
description: Seznamte se s tím, co se nachází v souboru web.config a jak nakonfigurovat různé možnosti ASP.NET Core modulu.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755277"
---
# <a name="webconfig-file"></a>`web.config` souborů

`web.config`Je soubor, který čte služba IIS a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pro konfiguraci aplikace hostované službou IIS.

## <a name="webconfig-file-location"></a>`web.config` umístění souboru

Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , `web.config` musí být soubor přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace. Toto je stejné umístění jako fyzická cesta k webu poskytované službě IIS. `web.config`Soubor se vyžaduje v kořenu aplikace, aby bylo možné publikování více aplikací pomocí nasazení webu.

Citlivé soubory existují na fyzické cestě aplikace, například `{ASSEMBLY}.runtimeconfig.json` `{ASSEMBLY}.xml` (dokumentační komentáře XML), a `{ASSEMBLY}.deps.json` , kde zástupný symbol `{ASSEMBLY}` je název sestavení. Když `web.config` je soubor přítomen a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje. Pokud `web.config` soubor chybí, nesprávně pojmenovaný nebo nedokáže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.

**Tento `web.config` soubor musí být v nasazení přítomen vždy, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte `web.config` soubor z produkčního nasazení.**

Pokud `web.config` soubor není v projektu přítomen, vytvoří se soubor se správnými `processPath` a `arguments` ke konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).

Pokud `web.config` je v projektu přítomen soubor, soubor je transformován se správnými `processPath` a `arguments` ke konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu. Transformace nemění nastavení konfigurace služby IIS v souboru.

`web.config`Soubor může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS. Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .

Vytváření, transformace a publikování `web.config` souboru je zpracováno nástrojem MSBuild cíl ( `_TransformWebConfig` ) při publikování projektu. Tento cíl je k dispozici v cíle webové sady SDK ( `Microsoft.NET.Sdk.Web` ). Sada SDK je nastavena v horní části souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Chcete-li webové sadě SDK zabránit ve transformaci tohoto `web.config` souboru, použijte `<IsTransformWebConfigDisabled>` vlastnost v souboru projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Při zakazování webové sady SDK z transformace souboru `processPath` `arguments` by měl být sada a nastavena ručně vývojářem. Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Konfigurace modulu ASP.NET Core s `web.config`

Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v `web.config` souboru lokality.

Následující `web.config` soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Následující `web.config` publikace jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications%2A>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.

Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` . Cesta uloží protokoly stdout do `LogFiles` složky, což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy `aspNetCore` elementu

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný řetězcový atribut.</p><p>Argumenty ke spustitelnému souboru určenému v `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 má nakonfigurovanou `web.config` prioritu.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, token se přepošle podřízenému procesu nasledujícímu `%ASPNETCORE_PORT%` jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost. Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</p> | `true` |
| `hostingModel` | <p>Volitelný řetězcový atribut.</p><p>Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut</p><p>Určuje počet instancí procesu zadaných v `processPath` nastavení, které je možné vystavit pro jednotlivé aplikace.</p><p>&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí `1`<br>Dlouhé `1`<br>Počet `100`&dagger; |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut</p><p>Určuje počet pokusů, kolikrát `processPath` je povoleno selhání procesu určeného v rámci jedné minuty. Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</p><p>Nepodporováno v hostování v rámci procesu.</p> | Výchozí `10`<br>Dlouhé `0`<br>Počet `100` |
| `requestTimeout` | <p>Volitelný atribut TimeSpan.</p><p>Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</p><p>Neplatí pro hostování v procesu. Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</p><p>Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59. Použití `60` hodnoty v minutách nebo sekundách má za následek *500 – interní chyba serveru*.</p> | Výchozí `00:02:00`<br>Dlouhé `00:00:00`<br>Počet `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když `app_offline.htm` se zjistí soubor</p> | Výchozí `10`<br>Dlouhé `0`<br>Počet `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá. Pokud je tento časový limit překročen, modul proces ukončuje.</p><p>Při hostování *v procesu*: proces **není restartován a** **nepoužívá** `rapidFailsPerMinute` nastavení.</p><p>Při hostování *mimo proces*: modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se aplikaci nepodaří spustit `rapidFailsPerMinute` několikrát během poslední minuty za minutu.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí `120`<br>Dlouhé `0`<br>Počet `3600` |
| `stdoutLogEnabled` | <p>Volitelný atribut typu Boolean.</p><p>Je-li nastavena hodnota true `stdout` a `stderr` pro proces určený v `processPath` je přesměrován do souboru určeného v `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>Volitelný řetězcový atribut.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který `stdout` a `stderr` z procesu určeného v části `processPath` jsou protokolovány. Relativní cesty jsou relativní vzhledem k kořenu webu. Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané. Pomocí oddělovačů podtržítka se `.log` do posledního segmentu cesty přidají časové razítko, ID procesu a Přípona souboru () `stdoutLogFile` . Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako `stdout_20180205194132_1934.log` ve `logs` složce při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v `processPath` atributu. Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce. Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.

Následující příklad nastaví dvě proměnné prostředí v `web.config` . `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` . Vývojář může tuto hodnotu dočasně nastavit v souboru, aby se `web.config` vynutila načítání [stránky výjimky pro vývojáře](xref:fundamentals/error-handling) při ladění výjimky aplikace. `CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Alternativou k nastavení prostředí přímo v `web.config` je zahrnutí `<EnvironmentName>` vlastnosti do [publikačního profilu ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v `web.config` okamžiku publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Konfigurace služby IIS s `web.config`

Konfigurace služby IIS je ovlivněna `<system.webServer>` sekcí `web.config` pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikací s modulem ASP.NET Core. Například konfigurace služby IIS je funkční pro dynamickou kompresi. Pokud je služba IIS nakonfigurovaná na úrovni serveru tak, aby používala dynamickou kompresi, `<urlCompression>` může element v souboru aplikace tuto `web.config` aplikaci pro ASP.NET Core zakázat.

Další informace najdete v následujících tématech:

* [Odkaz na konfiguraci pro `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Nastavení proměnných prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější) najdete v části * `AppCmd.exe` příkaz* v tématu věnovaném oddílům [prostředí `<environmentVariables>` ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci ke službě IIS.

## <a name="configuration-sections-of-webconfig"></a>Konfigurační oddíly pro `web.config`

Konfigurační oddíly aplikací ASP.NET 4. x `web.config` nepoužívá aplikace ASP.NET Core pro konfiguraci:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikace ASP.NET Core jsou nakonfigurovány pomocí jiných poskytovatelů konfigurace. Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Transformace souboru web.config

Pokud potřebujete transformovat `web.config` při publikování, přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig> . Možná budete muset transformovat `web.config` při publikování a nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí.

## <a name="additional-resources"></a>Další zdroje informací

* [SLUŽBU \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
