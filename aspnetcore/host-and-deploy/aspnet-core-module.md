---
title: Modul ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667298"
---
# <a name="aspnet-core-module"></a>Modul ASP.NET Core

Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)a [Justin](https://github.com/jkotalik) Kotalik

::: moniker range=">= aspnetcore-3.0"

Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:

* Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).
* Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu používá modul implementaci v rámci procesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).

Při hostování mimo proces funguje modul pouze s Kestrel. Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.

Při hostování v procesu platí následující vlastnosti:

* Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`). Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>:

  * Zaregistrujte `IISHttpServer`.
  * Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

* [Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.

* Sdílení fondem aplikací mezi aplikacemi se nepodporuje. Použijte jeden fond aplikací na aplikaci.

* Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

* Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.

* Odpojení klienta jsou zjištěny. Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.

* V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).

  Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Zavolejte metodu `SetCurrentDirectory`. Další volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.

* Při hostování v procesu není <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně volána k inicializaci uživatele. Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> volejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a přidejte ověřovací služby:

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

Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.

Namísto serveru HTTP IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .

Pro nedokončené volání [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> k:

* Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

### <a name="hosting-model-changes"></a>Hostování změny modelu

Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.

Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express. Další požadavek na aplikaci vytvoří nový proces IIS Express.

### <a name="process-name"></a>Název procesu

sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.

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
                  hostingModel="inprocess" />
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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false`, která označuje, že nastavení zadaná v [\<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `hostingModel` | <p>Volitelný atribut řetězce.</p><p>Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Max: `100`&dagger; |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p><p>Není podporováno s hostitelem v procesu.</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</p><p>Neplatí pro hostování v procesu. Hostování v procesu, čeká modul app ke zpracování požadavku.</p><p>Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59. Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Maximum: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Maximum: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořen webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce. Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.

Následující příklad nastaví dvě proměnné prostředí v *souboru Web. config*. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

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
> Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

## <a name="start-up-error-page"></a>Spuštění chybovou stránku

Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.

Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.

Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .

Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .

Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření protokolu a přesměrování

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`. Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování. Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.

Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.

Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace. Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky. Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Pokud je `stdoutLogEnabled` false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 KB. Po spuštění se zahodí všechny další protokoly.

Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`. Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.

Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.

Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly. Přidejte `<handlerSettings>` element do elementu `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:

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

Všechny složky v cestě (*protokoly* v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.

Úrovně (v pořadí od nejméně na nejpodrobnější):

* CHYBA
* UPOZORNĚNÍ
* INFORMACE O
* TRASOVÁNÍ

Umístění (umístění více jsou povoleny):

* KONZOLY
* PROTOKOL UDÁLOSTÍ
* SOUBOR

Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění. (Výchozí: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.

> [!WARNING]
> Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému. Velikost souboru protokolu není omezený. Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.

Příklad `aspNetCore` elementu v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .

## <a name="modify-the-stack-size"></a>Úprava velikosti zásobníku

*Platí pouze při použití modelu hostování v rámci procesu.*

Nakonfigurujte velikost spravovaného zásobníku pomocí nastavení `stackSize` v bajtech v *souboru Web. config*. Výchozí velikost je `1048576` bajtů (1 MB).

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token pro párování

*Platí pouze pro hostování mimo proces.*

Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.

Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy. Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí. Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut. Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server. Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Modul ASP.NET Core s službu IIS sdílenou konfiguraci

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.

Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:

1. Zakážete sdílenou konfiguraci IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a hostování sady Instalační protokoly

Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Modul, schéma a konfiguraci umístění souborů

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config

* *IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:

* Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS (`w3wp.exe`), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).
* Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).

Podporované verze systému Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu používá modul implementaci v rámci procesového serveru pro službu IIS, která se nazývá IIS HTTP Server (`IISHttpServer`).

Při hostování mimo proces funguje modul pouze s Kestrel. Modul nefunguje s [ovladačem HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostování v procesu

Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do souboru projektu aplikace vlastnost `<AspNetCoreHostingModel>` s hodnotou `InProcess` (hostování mimo procesy je nastaveno s `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.

Hodnota `<AspNetCoreHostingModel>` rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.

Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, je výchozí hodnota `OutOfProcess`.

Při hostování v procesu platí následující vlastnosti:

* Místo [Kestrel](xref:fundamentals/servers/kestrel) serveru se používá server HTTP služby IIS (`IISHttpServer`). Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>:

  * Zaregistrujte `IISHttpServer`.
  * Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

* [Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.

* Sdílení fondem aplikací mezi aplikacemi se nepodporuje. Použijte jeden fond aplikací na aplikaci.

* Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [souboru App_offline. htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

* Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.

* Odpojení klienta jsou zjištěny. Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.

* V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *W3wp. exe*).

  Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Zavolejte metodu `SetCurrentDirectory`. Další volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresář aplikace.

* Při hostování v procesu není <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> interně volána k inicializaci uživatele. Proto implementace <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována. Při transformaci deklarací s implementací <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> volejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a přidejte ověřovací služby:

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

* Nezadávejte vlastnost `<AspNetCoreHostingModel>`. Pokud se v souboru nenachází vlastnost `<AspNetCoreHostingModel>`, je výchozí hodnota `OutOfProcess`.
* Nastavte hodnotu vlastnosti `<AspNetCoreHostingModel>` na `OutOfProcess` (hostování v procesu je nastaveno pomocí `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` platné hodnoty.

Namísto serveru HTTP IIS (`IISHttpServer`) se používá server [Kestrel](xref:fundamentals/servers/kestrel) .

Pro nedokončené volání [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> k:

* Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.

### <a name="hosting-model-changes"></a>Hostování změny modelu

Pokud je nastavení `hostingModel` změněno v souboru *Web. config* (vysvětleno v části [Konfigurace pomocí Web. config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.

Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express. Další požadavek na aplikaci vytvoří nový proces IIS Express.

### <a name="process-name"></a>Název procesu

sestavy `Process.GetCurrentProcess().ProcessName` `w3wp`/`iisexpress` (v procesu) nebo `dotnet` (mimo proces).

Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní. Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core může také:

* Nastavte proměnné prostředí pro pracovní proces.
* Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.
* Předejte tokeny ověřování systému Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.

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
                  hostingModel="inprocess" />
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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Vlastnost <xref:System.Configuration.SectionInformation.InheritInChildApplications*> je nastavena na hodnotu `false`, která označuje, že nastavení zadaná v [\<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) nejsou děděna aplikacemi, které se nacházejí v podadresáři aplikace.

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `hostingModel` | <p>Volitelný atribut řetězce.</p><p>Určuje model hostování jako vnitroprocesové (`InProcess`/`inprocess`) nebo mimo proces (`OutOfProcess`/`outofprocess`).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>&dagger;pro hostování v rámci procesu je hodnota omezená na `1`.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Max: `100`&dagger; |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p><p>Není podporováno s hostitelem v procesu.</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</p><p>Neplatí pro hostování v procesu. Hostování v procesu, čeká modul app ke zpracování požadavku.</p><p>Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59. Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Maximum: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Maximum: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořen webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce. Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.

Následující příklad nastaví dvou proměnných prostředí. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

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
> Alternativou k nastavení prostředí přímo v souboru *Web. config* je zahrnutí vlastnosti `<EnvironmentName>` do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu. Tento přístup nastaví prostředí v *souboru Web. config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

## <a name="start-up-error-page"></a>Spuštění chybovou stránku

Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.

Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.

Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .

Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .

Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Vytvoření protokolu a přesměrování

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`. Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování. Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.

Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.

Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace. Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky. Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Pokud je `stdoutLogEnabled` false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 KB. Po spuštění se zahodí všechny další protokoly.

Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`. Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.

Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly. Přidejte `<handlerSettings>` element do elementu `<aspNetCore>` v *souboru Web. config*. Nastavení `debugLevel` na `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:

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

Složky v cestě zadané `<handlerSetting>` hodnotě (*protokoly* v předchozím příkladu) se nevytváří modulem automaticky a měly by být v nasazení předběžně existující. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Hodnoty úrovně ladění (`debugLevel`) mohou zahrnovat jak úroveň, tak i umístění.

Úrovně (v pořadí od nejméně na nejpodrobnější):

* CHYBA
* UPOZORNĚNÍ
* INFORMACE O
* TRASOVÁNÍ

Umístění (umístění více jsou povoleny):

* KONZOLY
* PROTOKOL UDÁLOSTÍ
* SOUBOR

Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; cestu k souboru protokolu ladění. (Výchozí: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; nastavení úrovně ladění.

> [!WARNING]
> Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému. Velikost souboru protokolu není omezený. Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.

Příklad `aspNetCore` elementu v souboru *Web. config* naleznete v tématu [Konfigurace pomocí souboru Web. config](#configuration-with-webconfig) .

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token pro párování

*Platí pouze pro hostování mimo proces.*

Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.

Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy. Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí. Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut. Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server. Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Modul ASP.NET Core s službu IIS sdílenou konfiguraci

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.

Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s parametrem `OPT_NO_SHARED_CONFIG_CHECK` nastaveným na `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:

1. Zakážete sdílenou konfiguraci IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a hostování sady Instalační protokoly

Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Modul, schéma a konfiguraci umístění souborů

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config

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

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

ASP.NET Core modul je nakonfigurovaný pomocí oddílu `aspNetCore` uzlu `system.webServer` v souboru *Web. config* webu.

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

Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile`á cesta je nastavená na `\\?\%home%\LogFiles\stdout`. Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.

Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty ke spustitelnému souboru určenému v **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Pokud je nastaveno na true, stránka **502,5 – selhání procesu** se potlačí a stavová stránka 502 konfigurovaná v *souboru Web. config* má přednost.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</p><p>Nastavení `processesPerApplication` se nedoporučuje. Tento atribut bude v budoucí verzi odebrán.</p> | Výchozí: `1`<br>Minimum: `1`<br>Maximum: `100` |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cesta začíná na `.`, je cesta považována za relativní vzhledem k kořenu webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo novějším, je `requestTimeout` zadána v hodinách, minutách a sekundách.</p> | Výchozí: `00:02:00`<br>Minimum: `00:00:00`<br>Maximum: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *App_offline. htm* .</p> | Výchozí: `10`<br>Minimum: `0`<br>Maximum: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</p><p>Hodnota 0 (nula **) není považována za** nekonečný časový limit.</p> | Výchozí: `120`<br>Minimum: `0`<br>Maximum: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno. Relativní cesty jsou relativní vzhledem k kořen webu. Všechny cesty začínající na `.` jsou relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty. Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu. Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ( *. log*). Pokud je jako hodnota zadána `.\logs\stdout`, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

Proměnné prostředí lze zadat pro proces v atributu `processPath`. Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem elementu `<environmentVariables>` kolekce.

> [!WARNING]
> Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem. Pokud je proměnná prostředí nastavena v souboru *Web. config* i na úrovni systému systému Windows, bude hodnota ze souboru *Web. config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development`), což zabrání spuštění aplikace.

Následující příklad nastaví dvou proměnných prostředí. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby bylo `Development`. Vývojář může tuto hodnotu dočasně nastavit v souboru *Web. config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace. `CONFIG_DIR` je příklad uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.

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
> Nastavte pouze proměnnou prostředí `ASPNETCORE_ENVIRONMENT`, aby `Development` na pracovních a testovacích serverech, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *App_offline. htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků. Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit`, modul ASP.NET Core ukončuje běžící proces.

Když je přítomen soubor *App_offline. htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu souboru *App_offline. htm* . Po odebrání souboru *App_offline. htm* aplikace spustí další požadavek.

## <a name="start-up-error-page"></a>Spuštění chybovou stránku

Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* . Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte atribut `disableStartUpErrorPage`. Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Vytvoření protokolu a přesměrování

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud jsou nastaveny atributy `stdoutLogEnabled` a `stdoutLogFile` elementu `aspNetCore`. Všechny složky v `stdoutLogFile` cestě jsou vytvořeny modulem při vytvoření souboru protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se zapisují protokoly (k poskytnutí oprávnění k zápisu použijte `IIS AppPool\<app_pool_name>`).

Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování. Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.

Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.

Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace. Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( *. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*) oddělené podtržítky. Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.

Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\`. Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Když publikujete aplikaci pro nasazení Azure App Service, Webová sada SDK nastaví hodnotu `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Pro aplikace hostované v Azure App Service je předdefinovaná proměnná prostředí `%home`.

Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.

Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token pro párování

Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP. Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.

Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje. Token párování se vytvoří a nastaví na proměnnou prostředí (`ASPNETCORE_TOKEN`) modulem. Párovací token je také nastaven na hlavičku (`MS-ASPNETCORE-TOKEN`) u každého požadavku proxy. Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí. Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut. Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server. Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Modul ASP.NET Core s službu IIS sdílenou konfiguraci

Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** . Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *ApplicationHost. config* ve sdílené složce.

Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:

1. Zakážete sdílenou konfiguraci IIS.
1. Spusťte instalační program.
1. Exportujte aktualizovaný soubor *ApplicationHost. config* do sdílené složky.
1. Znovu povolte sdílenou konfiguraci IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a hostování sady Instalační protokoly

Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:

1. V hostitelském systému přejděte na *%windir%\system32\inetsrv*.
1. Vyhledejte soubor *aspnetcore. dll* .
1. Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .
1. Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.

Protokoly instalačních balíčků pro modul jsou k dispozici v *C:\\uživatelé\\% username%\\data\\místní\\Temp*. Soubor má název *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Modul, schéma a konfiguraci umístění souborů

### <a name="module"></a>Modul

**Služba IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Schéma

**SLUŽBU**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Konfigurace

**SLUŽBU**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {ROOT aplikace}\\. vs\config\applicationHost.config

* *IISExpress. exe* CLI:%USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory lze najít hledáním *aspnetcore* v souboru *ApplicationHost. config* .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [Zdrojový odkaz na modul ASP.NET Core (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; použít rozevírací seznam **větve** k výběru konkrétní verze (například `release/3.1`).
* <xref:host-and-deploy/iis/modules>
