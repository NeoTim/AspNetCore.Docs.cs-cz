---
title: Modul ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak nakonfigurovat základní modul ASP.NET pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667298"
---
# <a name="aspnet-core-module"></a>Modul ASP.NET Core

[Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), a [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Základní modul ASP.NET je nativní modul iis, který se zapojuje do kanálu iis a provádí následující:

* Hostujte aplikaci ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), nazývanou model hostování v [procesu](#in-process-hosting-model).
* Přepošle webové požadavky do back-endu ASP.NET aplikaci Core se systémem [Kestrel server](xref:fundamentals/servers/kestrel), nazvaný [mimoprocesový model hostování](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu modul používá pro službu IIS implementaci serveru`IISHttpServer`v procesu nazvanou IIS HTTP Server ( ).

Při hostování mimo proces modul pracuje pouze s Kestrel. Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

ASP.NET základní aplikace výchozí pro model hostování v procesu.

Následující charakteristiky platí při hostování v průběhu procesu:

* Místo serveru`IISHttpServer` [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ( ). Pro v průběhu procesu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá na:

  * Zaregistrujte `IISHttpServer`soubor .
  * Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.
  * Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.

* Atribut [requestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v procesu.

* Sdílení fondu aplikací mezi aplikacemi není podporované. Použijte jeden fond aplikací pro jednu aplikaci.

* Při použití [nasazení na webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručním [umístěním souboru app_offline.htm do nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)nemusí být aplikace okamžitě ukončena, pokud existuje otevřené připojení. Například připojení websocket může zpozdit vypnutí aplikace.

* Architektura (bitness) aplikace a nainstalovaný runtime (x64 nebo x86) musí odpovídat architektuře fondu aplikací.

* Jsou zjištěna odpojení klienta. Token zrušení [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušen, když se klient odpojí.

* V ASP.NET jádrem 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS, nikoli adresář aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).

  Ukázkový kód, který nastavuje aktuální adresář aplikace, naleznete ve [třídě CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Volání `SetCurrentDirectory` metody. Následná <xref:System.IO.Directory.GetCurrentDirectory*> volání k poskytnutí adresáře aplikace.

* Při hostování v <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesu, není volána interně inicializovat uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace slouží k transformaci deklarací identity po každém ověřování není aktivován ve výchozím nastavení. Při transformaci deklarací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> identity <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> s implementací volejte a přidejte ověřovací služby:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [Nasazení webového balíčku (jednoho souboru) nejsou podporována.](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Chcete-li nakonfigurovat aplikaci pro mimoprocesový hosting, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti v `OutOfProcess` souboru projektu (*.csproj*):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hostování v procesu `InProcess`je nastaveno pomocí , což je výchozí hodnota.

Hodnota `<AspNetCoreHostingModel>` je malá a velká `inprocess` `outofprocess` písmena, tak a jsou platné hodnoty.

Místo http serveru služby IIS (`IISHttpServer`) se používá [poštovné](xref:fundamentals/servers/kestrel) server .

Pro mimoproces, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:

* Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.
* Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.

### <a name="hosting-model-changes"></a>Hostování změn modelu

Pokud `hostingModel` se nastavení změní v souboru *web.config* (vysvětleno v části [Konfigurace s web.config),](#configuration-with-webconfig) modul recykluje pracovní proces služby IIS.

Pro iis Express modul nerecykluje pracovní proces, ale místo toho aktivuje řádné vypnutí aktuálního procesu iis express. Další požadavek na aplikaci spouští nový proces služby IIS Express.

### <a name="process-name"></a>Název procesu

`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (v průběhu) nebo `dotnet` (mimoproces).

Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní. Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .

Základní modul ASP.NET může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.
* Přepošlete ověřovací tokeny systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat ASP.NET core modul

Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.

Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:

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

Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena tak, aby `false` označovala, že nastavení zadaná v [ \<rámci umístění>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) prvek nejsou zděděna aplikacemi, které jsou umístěny v podadresáři aplikace.

Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`. Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.

Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy prvku aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru zadanému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek. Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</p> | `true` |
| `hostingModel` | <p>Volitelný atribut řetězce.</p><p>Určuje hostitelský model jako v`InProcess`/`inprocess`procesu ( )`OutOfProcess`/`outofprocess`nebo mimo proces ( ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</p><p>&dagger;Pro hostování v procesu je `1`hodnota omezena na .</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude odebrán v budoucí verzi.</p> | Výchozí:`1`<br>Min:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP. Relativní cesty jsou podporovány. Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu. Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</p><p>Není podporováno s hostováním v procesu.</p> | Výchozí:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Volitelný atribut timespan.</p><p>Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</p><p>Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</p><p>Nevztahuje se na hostování v procesu. Pro hostování v procesu modul čeká na aplikaci ke zpracování požadavku.</p><p>Platné hodnoty pro minuty a sekundy segmenty řetězce jsou v rozsahu 0-59. Použití **60** v hodnotě minut nebo sekund má za následek *500 - Vnitřní chyba serveru*.</p> | Výchozí:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</p> | Výchozí:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu. Pokud je tento časový limit překročen, modul proces unejde. Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</p><p>Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</p> | Výchozí:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.** Relativní cesty jsou relativní ke kořenu webu. Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky uvedené v cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).* Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces `processPath` v atributu. Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce. Proměnné prostředí nastavené v této části mají přednost před proměnnými systémového prostředí.

Následující příklad nastaví dvě proměnné prostředí v *web.config*. `ASPNETCORE_ENVIRONMENT` konfiguruje prostředí `Development`aplikace tak, aby . Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace. `CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.

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
> Alternativou k nastavení prostředí přímo v *web.config* je zahrnout `<EnvironmentName>` vlastnost v profilu publikování [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline.htm

Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků. Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.

Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.* Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.

Při použití mimoproceshostingového modelu se aplikace nemusí okamžitě vypnout, pokud existuje otevřené připojení. Například připojení websocket může zpozdit vypnutí aplikace.

## <a name="start-up-error-page"></a>Chybová stránka při spuštění

Hostování v průběhu procesu i mimo proces vytváří vlastní chybové stránky, když se jim nepodaří spustit aplikaci.

Pokud ASP.NET základnímodul nenalezne obslužnou rutinu požadavku v procesu nebo mimo proces, zobrazí se stránka s kódem chyby selhání *zatížení 500.0 - In-Process/Out-Of-Process Handler.*

Pro hostování v procesu, pokud ASP.NET základní modul nepodaří spustit aplikaci, zobrazí se stránka stavového kódu *500.30 - Start Failure.*

Pro mimoproceshosting, pokud ASP.NET core modul se nezdaří spustit proces back-endu nebo proces back-endu spustí, ale nedokáže poslouchat na nakonfigurovaném portu, zobrazí se stránka s kódem stavového kódu *selhání procesu 502.5 - Process Failure.*

Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce se stavovým kódem iIS 5xx, použijte atribut. Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek. Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).

Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu. Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.

Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.

Nepoužívejte protokol stdout pro obecné účely protokolování aplikací. Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky. Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.

Pokud `stdoutLogEnabled` je false, chyby, ke kterým dojde při spuštění aplikace jsou zachyceny a vyzařovány do protokolu událostí až 30 KB. Po spuštění jsou všechny další protokoly zahozeny.

Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`. Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na . Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.

Chcete-li vytvořit pravidla filtru protokolování, přečtěte si části filtrování [konfigurace](xref:fundamentals/logging/index#log-filtering) a [protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci k protokolování jádra ASP.NET.

Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Základní modul ASP.NET je konfigurovatelný tak, aby poskytoval protokoly rozšířené diagnostiky. Přidejte `<handlerSettings>` prvek `<aspNetCore>` do prvku v *souboru web.config*. Nastavení `debugLevel` na `TRACE` vystavit vyšší věrnost diagnostických informací:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Všechny složky v cestě *(protokoly* v předchozím příkladu) jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).

Hodnoty úrovně`debugLevel`ladění ( ) mohou zahrnovat úroveň i umístění.

Úrovně (v pořadí od nejméně k většině verbose):

* ERROR
* WARNING
* Info
* TRACE

Umístění (více míst je povoleno):

* Konzoly
* Eventlog
* SOUBOR

Nastavení obslužné rutiny lze také poskytnout prostřednictvím proměnných prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Cesta k souboru protokolu ladění. (Výchozí: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`&ndash; Nastavení úrovně ladění.

> [!WARNING]
> **Nenechávejte** protokolování ladění povolené v nasazení déle, než je nutné k řešení problému. Velikost protokolu není omezena. Ponechání protokolu ladění povoleno může vyčerpat dostupné místo na disku a selhání serveru nebo služby aplikace.

Příklad `aspNetCore` prvku v souboru *web.config* naleznete v části Konfigurace [pomocí souboru web.config.](#configuration-with-webconfig)

## <a name="modify-the-stack-size"></a>Změna velikosti zásobníku

*Platí pouze při použití modelu hostování v procesu.*

Nakonfigurujte velikost `stackSize` spravovaného zásobníku pomocí nastavení v bajtů v *souboru web.config*. Výchozí velikost `1048576` je bajty (1 MB).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace serveru proxy používá protokol HTTP a token párování

*Platí pouze pro mimoprocesový hosting.*

Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.

Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje. Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem. Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku. Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí. Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta. Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server. Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET základní modul se sdílenou konfigurací služby IIS

Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.** Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.

Při použití sdílené konfigurace služby IIS ve stejném počítači jako instalace služby `OPT_NO_SHARED_CONFIG_CHECK` IIS `1`spusťte instalační program sady ASP.NET Core Hosting Bundle s parametrem nastaveným na :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte takto:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku

Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:

1. V hostitelském systému přejděte na *%windir%\System32\inetsrv*.
1. Vyhledejte soubor *aspnetcore.dll.*
1. Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**
1. Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.

Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění modulů, schémat a konfiguračních souborů

### <a name="module"></a>Modul

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Základní modul ASP.NET je nativní modul iis, který se zapojuje do kanálu iis a provádí následující:

* Hostujte aplikaci ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), nazývanou model hostování v [procesu](#in-process-hosting-model).
* Přepošle webové požadavky do back-endu ASP.NET aplikaci Core se systémem [Kestrel server](xref:fundamentals/servers/kestrel), nazvaný [mimoprocesový model hostování](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu modul používá pro službu IIS implementaci serveru`IISHttpServer`v procesu nazvanou IIS HTTP Server ( ).

Při hostování mimo proces modul pracuje pouze s Kestrel. Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Chcete-li nakonfigurovat aplikaci pro `<AspNetCoreHostingModel>` hostování v rámci procesu, přidejte `InProcess` vlastnost do souboru projektu `OutOfProcess`aplikace s hodnotou (mimoprocesový hosting je nastaven s ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Model hostování v procesu není podporován pro aplikace ASP.NET Core, které cílí na rozhraní .NET Framework.

Hodnota `<AspNetCoreHostingModel>` je malá a velká `inprocess` `outofprocess` písmena, tak a jsou platné hodnoty.

Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru k dispozici, `OutOfProcess`je výchozí hodnota .

Následující charakteristiky platí při hostování v průběhu procesu:

* Místo serveru`IISHttpServer` [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ( ). Pro v průběhu procesu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá na:

  * Zaregistrujte `IISHttpServer`soubor .
  * Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.
  * Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.

* Atribut [requestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v procesu.

* Sdílení fondu aplikací mezi aplikacemi není podporované. Použijte jeden fond aplikací pro jednu aplikaci.

* Při použití [nasazení na webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručním [umístěním souboru app_offline.htm do nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)nemusí být aplikace okamžitě ukončena, pokud existuje otevřené připojení. Například připojení websocket může zpozdit vypnutí aplikace.

* Architektura (bitness) aplikace a nainstalovaný runtime (x64 nebo x86) musí odpovídat architektuře fondu aplikací.

* Jsou zjištěna odpojení klienta. Token zrušení [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušen, když se klient odpojí.

* V ASP.NET jádrem 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS, nikoli adresář aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).

  Ukázkový kód, který nastavuje aktuální adresář aplikace, naleznete ve [třídě CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Volání `SetCurrentDirectory` metody. Následná <xref:System.IO.Directory.GetCurrentDirectory*> volání k poskytnutí adresáře aplikace.

* Při hostování v <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesu, není volána interně inicializovat uživatele. Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace slouží k transformaci deklarací identity po každém ověřování není aktivován ve výchozím nastavení. Při transformaci deklarací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> identity <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> s implementací volejte a přidejte ověřovací služby:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Chcete-li nakonfigurovat aplikaci pro mimoprocesový hosting, použijte v souboru projektu některý z následujících přístupů:

* Nezadávejte `<AspNetCoreHostingModel>` vlastnost. Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru k dispozici, `OutOfProcess`je výchozí hodnota .
* Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti `OutOfProcess` na (hostování v `InProcess`procesu je nastaveno):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hodnota je malá a `inprocess` velká `outofprocess` písmena, takže a jsou platné hodnoty.

Místo http serveru služby IIS (`IISHttpServer`) se používá [poštovné](xref:fundamentals/servers/kestrel) server .

Pro mimoproces, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:

* Nakonfigurujte port a základní cestu, kterou by měl server poslouchat při spuštění za ASP.NET základního modulu.
* Nakonfigurujte hostitele tak, aby zaznamenával chyby při spuštění.

### <a name="hosting-model-changes"></a>Hostování změn modelu

Pokud `hostingModel` se nastavení změní v souboru *web.config* (vysvětleno v části [Konfigurace s web.config),](#configuration-with-webconfig) modul recykluje pracovní proces služby IIS.

Pro iis Express modul nerecykluje pracovní proces, ale místo toho aktivuje řádné vypnutí aktuálního procesu iis express. Další požadavek na aplikaci spouští nový proces služby IIS Express.

### <a name="process-name"></a>Název procesu

`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (v průběhu) nebo `dotnet` (mimoproces).

Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní. Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .

Základní modul ASP.NET může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.
* Přepošlete ověřovací tokeny systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat ASP.NET core modul

Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.

Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:

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

Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena tak, aby `false` označovala, že nastavení zadaná v [ \<rámci umístění>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) prvek nejsou zděděna aplikacemi, které jsou umístěny v podadresáři aplikace.

Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`. Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.

Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy prvku aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru zadanému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek. Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</p> | `true` |
| `hostingModel` | <p>Volitelný atribut řetězce.</p><p>Určuje hostitelský model jako v`InProcess`/`inprocess`procesu ( )`OutOfProcess`/`outofprocess`nebo mimo proces ( ).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</p><p>&dagger;Pro hostování v procesu je `1`hodnota omezena na .</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude odebrán v budoucí verzi.</p> | Výchozí:`1`<br>Min:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP. Relativní cesty jsou podporovány. Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu. Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</p><p>Není podporováno s hostováním v procesu.</p> | Výchozí:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Volitelný atribut timespan.</p><p>Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</p><p>Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</p><p>Nevztahuje se na hostování v procesu. Pro hostování v procesu modul čeká na aplikaci ke zpracování požadavku.</p><p>Platné hodnoty pro minuty a sekundy segmenty řetězce jsou v rozsahu 0-59. Použití **60** v hodnotě minut nebo sekund má za následek *500 - Vnitřní chyba serveru*.</p> | Výchozí:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</p> | Výchozí:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu. Pokud je tento časový limit překročen, modul proces unejde. Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</p><p>Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</p> | Výchozí:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.** Relativní cesty jsou relativní ke kořenu webu. Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky uvedené v cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).* Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces `processPath` v atributu. Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce. Proměnné prostředí nastavené v této části mají přednost před proměnnými systémového prostředí.

Následující příklad nastaví dvě proměnné prostředí. `ASPNETCORE_ENVIRONMENT`konfiguruje prostředí `Development`aplikace tak, aby . Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace. `CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.

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
> Alternativou k nastavení prostředí přímo v *web.config* je zahrnout `<EnvironmentName>` vlastnost v profilu publikování [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *web.config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline.htm

Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků. Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.

Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.* Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.

Při použití mimoproceshostingového modelu se aplikace nemusí okamžitě vypnout, pokud existuje otevřené připojení. Například připojení websocket může zpozdit vypnutí aplikace.

## <a name="start-up-error-page"></a>Chybová stránka při spuštění

Hostování v průběhu procesu i mimo proces vytváří vlastní chybové stránky, když se jim nepodaří spustit aplikaci.

Pokud ASP.NET základnímodul nenalezne obslužnou rutinu požadavku v procesu nebo mimo proces, zobrazí se stránka s kódem chyby selhání *zatížení 500.0 - In-Process/Out-Of-Process Handler.*

Pro hostování v procesu, pokud ASP.NET základní modul nepodaří spustit aplikaci, zobrazí se stránka stavového kódu *500.30 - Start Failure.*

Pro mimoproceshosting, pokud ASP.NET core modul se nezdaří spustit proces back-endu nebo proces back-endu spustí, ale nedokáže poslouchat na nakonfigurovaném portu, zobrazí se stránka s kódem stavového kódu *selhání procesu 502.5 - Process Failure.*

Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce se stavovým kódem iIS 5xx, použijte atribut. Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek. Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).

Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu. Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.

Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.

Nepoužívejte protokol stdout pro obecné účely protokolování aplikací. Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky. Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.

Pokud `stdoutLogEnabled` je false, chyby, ke kterým dojde při spuštění aplikace jsou zachyceny a vyzařovány do protokolu událostí až 30 KB. Po spuštění jsou všechny další protokoly zahozeny.

Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`. Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na . Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.

Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Základní modul ASP.NET je konfigurovatelný tak, aby poskytoval protokoly rozšířené diagnostiky. Přidejte `<handlerSettings>` prvek `<aspNetCore>` do prvku v *souboru web.config*. Nastavení `debugLevel` na `TRACE` vystavit vyšší věrnost diagnostických informací:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Složky v cestě k `<handlerSetting>` hodnotě *(protokoly* v předchozím příkladu) nejsou vytvořeny modulem automaticky a měly by existovat předem v nasazení. Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).

Hodnoty úrovně`debugLevel`ladění ( ) mohou zahrnovat úroveň i umístění.

Úrovně (v pořadí od nejméně k většině verbose):

* ERROR
* WARNING
* Info
* TRACE

Umístění (více míst je povoleno):

* Konzoly
* Eventlog
* SOUBOR

Nastavení obslužné rutiny lze také poskytnout prostřednictvím proměnných prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Cesta k souboru protokolu ladění. (Výchozí: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`&ndash; Nastavení úrovně ladění.

> [!WARNING]
> **Nenechávejte** protokolování ladění povolené v nasazení déle, než je nutné k řešení problému. Velikost protokolu není omezena. Ponechání protokolu ladění povoleno může vyčerpat dostupné místo na disku a selhání serveru nebo služby aplikace.

Příklad `aspNetCore` prvku v souboru *web.config* naleznete v části Konfigurace [pomocí souboru web.config.](#configuration-with-webconfig)

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace serveru proxy používá protokol HTTP a token párování

*Platí pouze pro mimoprocesový hosting.*

Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.

Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje. Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem. Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku. Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí. Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta. Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server. Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET základní modul se sdílenou konfigurací služby IIS

Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.** Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.

Při použití sdílené konfigurace služby IIS ve stejném počítači jako instalace služby `OPT_NO_SHARED_CONFIG_CHECK` IIS `1`spusťte instalační program sady ASP.NET Core Hosting Bundle s parametrem nastaveným na :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte takto:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku

Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:

1. V hostitelském systému přejděte na *%windir%\System32\inetsrv*.
1. Vyhledejte soubor *aspnetcore.dll.*
1. Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**
1. Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.

Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění modulů, schémat a konfiguračních souborů

### <a name="module"></a>Modul

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Základní modul ASP.NET je nativní modul služby IIS, který se zapojuje do kanálu služby IIS a předává webové požadavky do backendu ASP.NET aplikací core.

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Modul pracuje pouze s Kestrelem. Modul není kompatibilní se [souborem HTTP.sys](xref:fundamentals/servers/httpsys).

Vzhledem k tomu, že aplikace ASP.NET Core běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud dojde k chybě. Jedná se v podstatě o stejné chování jako u aplikací ASP.NET 4.x, které běží v procesu ve službě IIS, které jsou spravovány [službou aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikací:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra. Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

Modul určuje port prostřednictvím proměnné prostředí při spuštění a [middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal . `http://localhost:{port}` Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.

Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware. Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace. Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel. Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.

Mnoho nativních modulů, například ověřování systému Windows, zůstává aktivní. Další informace o modulech služby IIS aktivních <xref:host-and-deploy/iis/modules>v modulu ASP.NET Core naleznete v tématu .

Základní modul ASP.NET může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokolovat výstup stdout do úložiště souborů pro řešení problémů se spuštěním.
* Přepošlete ověřovací tokeny systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat ASP.NET core modul

Pokyny k instalaci ASP.NET základního modulu naleznete [v tématu Instalace sady .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

Základní modul ASP.NET je `aspNetCore` konfigurován s `system.webServer` částí uzlu v souboru *web.config* webu.

Následující soubor *web.config* je publikován pro [nasazení závislé na rámci](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje ASP.NET core modul pro zpracování požadavků webu:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Následující *web.config* je publikován pro [samostatné nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Když se aplikace nasadí do `stdoutLogFile` [služby Azure App Service](https://azure.microsoft.com/services/app-service/), je cesta nastavená na `\\?\%home%\LogFiles\stdout`. Cesta uloží protokoly stdout do složky *LogFiles,* což je umístění automaticky vytvořené službou.

Informace o konfiguraci podaplikací služby IIS naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy prvku aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru zadanému v **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud true, **502.5 - Selhání procesu** stránka je potlačena a 502 stavový kód stránky nakonfigurované v *web.config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Pokud true, token je předán podřízený proces naslouchání na %ASPNETCORE_PORT% jako záhlaví 'MS-ASPNETCORE-WINAUTHTOKEN' na požadavek. Je odpovědností tohoto procesu volání CloseHandle na tento token na požadavek.</p> | `true` |
| `processesPerApplication` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath,** které lze přetavit podle aplikace.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude odebrán v budoucí verzi.</p> | Výchozí:`1`<br>Min:`1`<br>Max:`100` |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spustí proces naslouchání pro požadavky HTTP. Relativní cesty jsou podporovány. Pokud cesta začíná `.`, cesta je považována za relativní ke kořenovému adresáři webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný atribut celé číslo.</p><p>Určuje počet, kolikrát je povoleno selhání procesu zadaného v **processPath** za minutu. Pokud je tento limit překročen, modul přestane spouštět proces po zbytek minuty.</p> | Výchozí:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Volitelný atribut timespan.</p><p>Určuje dobu, po kterou základní modul ASP.NET čeká na odpověď z procesu naslouchání na %ASPNETCORE_PORT%.</p><p>Ve verzích ASP.NET core modul, který byl dodán s vydáním ASP.NET `requestTimeout` Core 2.1 nebo novější, je zadán v hodinách, minutách a sekundách.</p> | Výchozí:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když je zjištěn soubor *app_offline.htm.*</p> | Výchozí:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Volitelný atribut celé číslo.</p><p>Doba trvání v sekundách, po kterou modul čeká na spuštění spustitelného souboru, který spustí proces naslouchání na portu. Pokud je tento časový limit překročen, modul proces unejde. Modul se pokusí znovu spustit proces, když obdrží nový požadavek a nadále se pokouší restartovat proces na následné příchozí požadavky, pokud aplikace se nezdaří spustit **rapidFailsPerMinute** počet opakování v poslední rolling minute.</p><p>Hodnota 0 (nula) **se nepovažuje** za nekonečný časový rozsah.</p> | Výchozí:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Pokud true, **stdout** a **stderr** pro proces zadaný v **processPath** jsou přesměrovány na soubor zadaný v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro kterou jsou protokolovány **stdout** a **stderr** z procesu určeného v **processPath.** Relativní cesty jsou relativní ke kořenu webu. Každá cesta `.` začínající jsou relativní ke kořenu webu a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky uvedené v cestě musí existovat, aby modul mohl vytvořit soubor protokolu. Pomocí oddělovačů podtržítek se do posledního segmentu cesty **stdoutLogFile** přidají časové razítko, ID procesu a přípona souboru (*.log).* Pokud `.\logs\stdout` je zadán jako hodnota, příklad stdout log je uložen jako *stdout_20180205194132_1934.log* ve složce *protokoly* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces `processPath` v atributu. Zadejte proměnnou `<environmentVariable>` prostředí s `<environmentVariables>` podřízeným prvkem prvku kolekce.

> [!WARNING]
> Proměnné prostředí nastavené v této části jsou v konfliktu s proměnnými systémového prostředí nastavenými se stejným názvem. Pokud je proměnná prostředí nastavena jak v souboru *web.config,* `ASPNETCORE_ENVIRONMENT: Development;Development`tak na systémové úrovni v systému Windows, hodnota ze souboru *web.config* se připojí k hodnotě proměnné systémového prostředí (například ), která brání spuštění aplikace.

Následující příklad nastaví dvě proměnné prostředí. `ASPNETCORE_ENVIRONMENT`konfiguruje prostředí `Development`aplikace tak, aby . Vývojář může dočasně nastavit tuto hodnotu v souboru *web.config,* aby se [stránka výjimky vývojáře načetla](xref:fundamentals/error-handling) při ladění výjimky aplikace. `CONFIG_DIR`je příkladem uživatelem definované proměnné prostředí, kde vývojář napsal kód, který při spuštění přečte hodnotu a vytvoří cestu pro načtení konfiguračního souboru aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Proměnnou `ASPNETCORE_ENVIRONMENT` prostředí nastavte `Development` pouze na pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline.htm

Pokud je v kořenovém adresáři aplikace zjištěn soubor s názvem *app_offline.htm,* pokusí se ASP.NET základní modul řádně vypnout aplikaci a zastavit zpracování příchozích požadavků. Pokud je aplikace stále spuštěna po `shutdownTimeLimit`počtu sekund definovaném v aplikaci , ASP.NET základní modul unejde spuštěný proces.

Zatímco soubor *app_offline.htm* je k dispozici, ASP.NET Core Module reaguje na požadavky odesláním zpět obsah souboru *app_offline.htm.* Po odebrání souboru *app_offline.htm* spustí další požadavek aplikaci.

## <a name="start-up-error-page"></a>Chybová stránka při spuštění

Pokud se ASP.NET základnímodul nespustí proces back-endu nebo se spustí proces back-endu, ale na konfigurovaném portu nenaslouchá, zobrazí se stránka se stavovým kódem *selhání procesu 502.5 - Process Failure.* Chcete-li tuto stránku potlačit a vrátit se k výchozí `disableStartUpErrorPage` stránce stavového kódu iIS 502, použijte atribut. Další informace o konfiguraci vlastních chybových zpráv naleznete v [tématu Http Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![502.5 Stránka kódu stavu selhání procesu](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

ASP.NET Core Module přesměruje výstup konzoly stdout a `stdoutLogEnabled` `stdoutLogFile` stderr `aspNetCore` na disk, pokud jsou nastaveny atributy a prvek. Všechny složky `stdoutLogFile` v cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít přístup pro zápis do umístění, `IIS AppPool\<app_pool_name>` kde jsou protokoly zapsány (slouží k poskytnutí oprávnění k zápisu).

Protokoly nejsou otočeny, pokud nedojde k recyklaci nebo restartování procesu. Je odpovědností hoster omezit místo na disku protokoly spotřebovávají.

Použití protokolu stdout se doporučuje pouze pro řešení problémů se spuštěním aplikace při hostování ve službě IIS nebo při použití [podpory služby IIS v době vývoje se službou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při místním ladění a spuštění aplikace se službou IIS Express.

Nepoužívejte protokol stdout pro obecné účely protokolování aplikací. Pro rutinní protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otáčí protokoly. Další informace naleznete [v tématu zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a přípona souboru jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony `stdoutLogFile` souboru (*.log*) k poslednímu segmentu cesty (obvykle *stdout)* vymezeném podtržítky. Pokud `stdoutLogFile` cesta končí *stdout*, protokol pro aplikaci s PID 1934 vytvořený na 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934.log*.

Následující ukázkový `aspNetCore` prvek konfiguruje protokolování stdout na relativní cestě `.\log\`. Zkontrolujte, zda má identita uživatele AppPoolu oprávnění k zápisu do poskytnuté cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Při publikování aplikace pro nasazení služby Azure App `stdoutLogFile` Service `\\?\%home%\LogFiles\stdout`nastaví sada Web SDK hodnotu na . Proměnná `%home` prostředí je předdefinovaná pro aplikace hostované službou Azure App Service.

Chcete-li vytvořit pravidla filtru protokolování, přečtěte si části filtrování [konfigurace](xref:fundamentals/logging/index#log-filtering) a [protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci k protokolování jádra ASP.NET.

Další informace o formátech cest naleznete v [tématu Formátcesty k souborům v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace serveru proxy používá protokol HTTP a token párování

Proxy server vytvořený mezi ASP.NET Core Module a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposlouchávání provozu mezi modulem a Kestrel z umístění mimo server.

Token párování se používá k zajištění, že požadavky přijaté Kestrel byly proxied službou IIS a nepochází z jiného zdroje. Token párování je vytvořen a nastaven do`ASPNETCORE_TOKEN`proměnné prostředí ( ) modulem. Token párování je také nastaven do`MS-ASPNETCORE-TOKEN`záhlaví ( ) na každém proxied požadavku. Služby IIS Middleware zkontroluje každý požadavek, který obdrží, a potvrdí, že hodnota záhlaví párování tokenu odpovídá hodnotě proměnné prostředí. Pokud jsou hodnoty tokenu neodpovídající, požadavek je zaznamenána a odmítnuta. Proměnná prostředí tokenu párování a přenosy mezi modulem a Kestrelem nejsou přístupné z umístění mimo server. Bez znalosti hodnoty tokenu párování nemůže útočník odeslat požadavky, které by obcéme kontroly v middlewaru služby IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET základní modul se sdílenou konfigurací služby IIS

Instalační program ASP.NET Core Module běží s oprávněními účtu **TrustedInstaller.** Vzhledem k tomu, že místní systémový účet nemá oprávnění ke změně cesty sdílení používané sdílenou konfigurací služby IIS, instalační program vyvolá chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.

Při použití sdílené konfigurace služby IIS postupujte takto:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Protokoly instalačního programu verze modulu a instalačního programu hostingového balíčku

Chcete-li zjistit verzi nainstalovaného ASP.NET základního modulu:

1. V hostitelském systému přejděte na *%windir%\System32\inetsrv*.
1. Vyhledejte soubor *aspnetcore.dll.*
1. Klepněte pravým tlačítkem myši na soubor a v kontextové nabídce vyberte **Vlastnosti.**
1. Vyberte kartu **Podrobnosti.** **Verze souboru** a **verze produktu** představují nainstalovanou verzi modulu.

Protokoly instalačního programu instalačního programu instalačního programu sady Hosting bundle pro modul naleznete na adrese *\\C:\\Users %UserName%\\AppData\\Local\\Temp*. Soubor je pojmenován *dd_DotNetCoreWinSvrHosting__\<časové razítko>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění modulů, schémat a konfiguračních souborů

### <a name="module"></a>Modul

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Schéma

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Konfigurace

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *Iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze nalézt hledáním *aspnetcore* v souboru *applicationHost.config.*

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET zdroj odkazu základního modulu (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Pomocí rozevíracího seznamu **Větev** vyberte konkrétní verzi `release/3.1`(například).
* <xref:host-and-deploy/iis/modules>
