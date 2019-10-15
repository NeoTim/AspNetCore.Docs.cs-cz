---
title: Modul ASP.NET Core
author: guardrex
description: Naučte se konfigurovat modul ASP.NET Core pro hostování ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333896"
---
# <a name="aspnet-core-module"></a>Modul ASP.NET Core

Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)a [Luke](https://github.com/guardrex) Latham

::: moniker range=">= aspnetcore-3.0"

Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:

* Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).
* Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).

Při hostování mimo proces funguje modul pouze s Kestrel. Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.

Následující vlastnosti platí při hostování v procesu:

* Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`). Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:

  * Zaregistrujte `IISHttpServer`.
  * Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

* [Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.

* Sdílení fondu aplikací mezi aplikacemi není podporováno. Použijte jeden fond aplikací na aplikaci.

* Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení. Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.

* Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.

* Zjistila se odpojení klienta. Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.

* V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).

  Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Zavolejte metodu `SetCurrentDirectory`. Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.

* Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele. Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:

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
  
  * [Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.

### <a name="out-of-process-hosting-model"></a>Model hostování mimo proces

Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` v souboru projektu ( *. csproj*):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hostování v rámci procesu je nastaveno pomocí `InProcess`, což je výchozí hodnota.

Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .

Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:

* Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

### <a name="hosting-model-changes"></a>Změny modelu hostování

Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.

V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express. Další požadavek na aplikaci vytvoří nový proces IIS Express.

### <a name="process-name"></a>Název procesu

sestavy `Process.GetCurrentProcess().ProcessName` `w3wp` @ no__t-2 @ no__t-3 (v procesu) nebo `dotnet` (mimo proces).

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru Web. config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.

Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný řetězcový atribut.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání. Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</p> | `true` |
| `hostingModel` | <p>Volitelný řetězcový atribut.</p><p>Určuje model hostování jako vnitroprocesové (`InProcess`) nebo mimo proces (`OutOfProcess`).</p> | `InProcess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>@no__t – 0For hostování v procesu, hodnota je omezená na `1`.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Max: `100` @ no__t-1 |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</p><p>Nepodporováno v hostování v rámci procesu.</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `100` |
| `requestTimeout` | <p>Volitelný atribut TimeSpan.</p><p>Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</p><p>Neplatí pro hostování v procesu. Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</p><p>Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59. Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá. Pokud je tento časový limit překročen, modul proces ukončuje. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný atribut typu Boolean.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný řetězcový atribut.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořenu webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`. Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.

Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline. htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení. Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.

## <a name="start-up-error-page"></a>Spouštěcí chybová stránka

Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.

Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.

Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .

Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .

Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`. Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu. Je zodpovědností hostitele omezit místo na disku, které používají protokoly.

Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací. Nepoužívejte protokol stdout pro obecné účely protokolování aplikace. Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky. Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB. Po spuštění budou všechny další protokoly zahozeny.

Následující vzorový @no__t element-0 v souboru *Web. config* nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service. Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování. Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly. Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.

Úrovně (v pořadí od nejméně po nejpodrobnější):

* Chyba
* Upozornění
* PŘÍJEMCE
* TRACE

Umístění (je povoleno více umístění):

* STROMU
* EVENTLOG
* SOUBORŮ

Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cesta k souboru protokolu ladění. (Výchozí: *aspnetcore-Debug. log*)
* nastavení úrovně ladění `ASPNETCORE_MODULE_DEBUG` &ndash;.

> [!WARNING]
> Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému. Velikost protokolu není omezená. Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.

Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .

## <a name="modify-the-stack-size"></a>Úprava velikosti zásobníku

*Platí pouze při použití modelu hostování v rámci procesu.*

Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajty (1 MB).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token párování.

*Platí pouze pro hostování mimo proces.*

Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP. Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.

Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku. Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí. Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne. Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server. Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET Core modul se sdílenou konfigurací služby IIS

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.

Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a instalační protokoly hostitelské sady

Postup určení verze nainstalovaného modulu ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění souboru modulu, schématu a konfiguračního souboru

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config

* *IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:

* Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`) se nazývá [model hostování v rámci procesu](#in-process-hosting-model).
* Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).

Při hostování mimo proces funguje modul pouze s Kestrel. Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastavena s `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.

Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.

Následující vlastnosti platí při hostování v procesu:

* Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`). Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> do:

  * Zaregistrujte `IISHttpServer`.
  * Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

* [Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.

* Sdílení fondu aplikací mezi aplikacemi není podporováno. Použijte jeden fond aplikací na aplikaci.

* Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručnímu umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení. Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.

* Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.

* Zjistila se odpojení klienta. Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.

* V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).

  Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Zavolejte metodu `SetCurrentDirectory`. Následná volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.

* Při hostování v procesu se nevolá <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně pro inicializaci uživatele. Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pro přidání ověřovacích služeb:

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

Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:

* Nezadávejte vlastnost `<AspNetCoreHostingModel>`. Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, výchozí hodnota je `OutOfProcess`.
* Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Místo HTTP serveru IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .

Pro mimoprocesové aplikace [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:

* Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

### <a name="hosting-model-changes"></a>Změny modelu hostování

Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.

V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express. Další požadavek na aplikaci vytvoří nový proces IIS Express.

### <a name="process-name"></a>Název procesu

sestavy `Process.GetCurrentProcess().ProcessName` `w3wp` @ no__t-2 @ no__t-3 (v procesu) nebo `dotnet` (mimo proces).

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru Web. config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.

Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false` k označení toho, že nastavení zadaná v rámci [> prvku \<location](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný řetězcový atribut.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání. Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</p> | `true` |
| `hostingModel` | <p>Volitelný řetězcový atribut.</p><p>Určuje model hostování jako vnitroprocesové (`InProcess`) nebo mimo proces (`OutOfProcess`).</p> | `OutOfProcess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>@no__t – 0For hostování v procesu, hodnota je omezená na `1`.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Max: `100` @ no__t-1 |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</p><p>Nepodporováno v hostování v rámci procesu.</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `100` |
| `requestTimeout` | <p>Volitelný atribut TimeSpan.</p><p>Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</p><p>Neplatí pro hostování v procesu. Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</p><p>Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59. Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá. Pokud je tento časový limit překročen, modul proces ukončuje. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný atribut typu Boolean.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný řetězcový atribut.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořenu webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`. Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.

Následující příklad nastaví dvě proměnné prostředí. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do profilu publikování ( *. pubxml*) nebo souboru projektu. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline. htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení. Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.

## <a name="start-up-error-page"></a>Spouštěcí chybová stránka

Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.

Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.

Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .

Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .

Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`. Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu. Je zodpovědností hostitele omezit místo na disku, které používají protokoly.

Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací. Nepoužívejte protokol stdout pro obecné účely protokolování aplikace. Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky. Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Pokud je hodnota `stdoutLogEnabled` false, budou chyby, ke kterým dojde při spuštění aplikace, zachyceny a generovány do protokolu událostí až do 30 KB. Po spuštění budou všechny další protokoly zahozeny.

Následující vzorový prvek `aspNetCore` nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service. Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování. Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly. Přidejte `<handlerSettings>` elementu do prvku `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.

Úrovně (v pořadí od nejméně po nejpodrobnější):

* Chyba
* Upozornění
* PŘÍJEMCE
* TRACE

Umístění (je povoleno více umístění):

* STROMU
* EVENTLOG
* SOUBORŮ

Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cesta k souboru protokolu ladění. (Výchozí: *aspnetcore-Debug. log*)
* nastavení úrovně ladění `ASPNETCORE_MODULE_DEBUG` &ndash;.

> [!WARNING]
> Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému. Velikost protokolu není omezená. Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.

Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token párování.

*Platí pouze pro hostování mimo proces.*

Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP. Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.

Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku. Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí. Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne. Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server. Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET Core modul se sdílenou konfigurací služby IIS

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.

Pokud používáte sdílenou konfiguraci služby IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a instalační protokoly hostitelské sady

Postup určení verze nainstalovaného modulu ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění souboru modulu, schématu a konfiguračního souboru

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config

* *IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Modul funguje pouze s Kestrel. Modul není kompatibilní s [http. sys](xref:fundamentals/servers/httpsys).

Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů. Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě. To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra. Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS). Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.

Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`. Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty. Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.

Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core. Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace. Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel. Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru Web. config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` v uzlu `system.webServer` v souboru *Web. config* tohoto webu.

Následující soubor *Web. config* je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a nakonfiguruje modul ASP.NET Core pro zpracování požadavků lokality:

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

Následující soubor *Web. config* je publikován pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), cesta `stdoutLogFile` je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný řetězcový atribut.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný atribut typu Boolean.</p><p>Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako hlavička MS-ASPNETCORE-WINAUTHTOKEN na vyžádání. Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</p> | `true` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Max: `100` |
| `processPath` | <p>Povinný atribut řetězce.</p><p>Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, bude cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `100` |
| `requestTimeout` | <p>Volitelný atribut TimeSpan.</p><p>Určuje dobu, po kterou modul ASP.NET Core čeká na odezvu od procesu, na kterém naslouchá% ASPNETCORE_PORT%.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, je `requestTimeout` zadáno v hodinách, minutách a sekundách.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut</p><p>Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá. Pokud je tento časový limit překročen, modul proces ukončuje. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný atribut typu Boolean.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný řetězcový atribut.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořenu webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána hodnota `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s podřízeným prvkem `<environmentVariable>` elementu kolekce `<environmentVariables>`.

> [!WARNING]
> Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem. Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), která brání aplikaci. začátek.

Následující příklad nastaví dvě proměnné prostředí. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

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
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT` na `Development` na pracovních a testovacích serverech, které nejsou přístupné pro nedůvěryhodné sítě, jako je například Internet.

## <a name="app_offlinehtm"></a>App_offline. htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

## <a name="start-up-error-page"></a>Spouštěcí chybová stránka

Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* . Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

![Stavová stránka selhání procesu procesu 502,5](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` prvku `aspNetCore`. Při vytvoření souboru protokolu jsou všechny složky v cestě `stdoutLogFile` vytvářeny modulem. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu. Je zodpovědností hostitele omezit místo na disku, které používají protokoly.

Použití protokolu stdout se doporučuje jenom při řešení problémů se spouštěním aplikací. Nepoužívejte protokol stdout pro obecné účely protokolování aplikace. Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru ( *. log*) k poslednímu segmentu cesty `stdoutLogFile` (obvykle *stdout*), oddělený podtržítky. Pokud cesta `stdoutLogFile` končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Následující vzorový prvek `aspNetCore` nakonfiguruje protokolování stdout pro aplikaci hostovanou v Azure App Service. Místní cesta nebo cesta ke sdílené síťové složce je přijatelná pro místní protokolování. Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

Složky v cestě zadané pro hodnotu `<handlerSetting>` (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Příklad prvku `aspNetCore` v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token párování.

Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP. Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.

Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párování tokenu je také nastaveno na hlavičku (`MS-ASPNETCORE-TOKEN`) na každém proxy požadavku. Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí. Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne. Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server. Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET Core modul se sdílenou konfigurací služby IIS

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* v sdílená složka.

Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:

1. Zakažte sdílenou konfiguraci služby IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci služby IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a instalační protokoly hostitelské sady

Postup určení verze nainstalovaného modulu ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul se nacházejí v *C: \\Users @ no__t-2% username% \\AppData @ no__t-4Local @ no__t-5Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__ @ no__t-7timestamp > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Umístění souboru modulu, schématu a konfiguračního souboru

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace} \\. vs\config\applicationHost.config

* *IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* [Úložiště GitHub modulu ASP.NET Core (odkazový zdroj)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
