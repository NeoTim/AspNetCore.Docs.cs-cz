---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 9270d7b462bbac1ae0ad896c0937ea6dd909b2cd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159552"
---
# <a name="aspnet-core-module"></a>Modul ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik), a [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-2.2"

Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:

* Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).
* Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).

Podporované verze Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Při hostování v procesu, modul používá v procesu serveru implementace pro službu IIS, volá HTTP serveru služby IIS (`IISHttpServer`).

Při hostování mimo proces, modul funguje pouze s Kestrel. Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modely hostingu

### <a name="in-process-hosting-model"></a>Model hostingu v procesu

Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace s hodnotou `InProcess` (hostování mimo proces se nastaví pomocí `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.

Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.

Při hostování v procesu platí následující vlastnosti:

* Server služby IIS protokolu HTTP (`IISHttpServer`) se použije namísto [Kestrel](xref:fundamentals/servers/kestrel) serveru. V procesu [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> na:

  * Zaregistrujte `IISHttpServer`.
  * Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.
  * Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.

* [RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.

* Sdílení fondem aplikací mezi aplikacemi se nepodporuje. Použijte jeden fond aplikací na aplikaci.

* Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

* Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.

* Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`. Pokud je obrácený pořadí, hostitel se nepodaří spustit.

* Odpojení klienta jsou zjištěny. [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.

* <xref:System.IO.Directory.GetCurrentDirectory*> Vrátí adresáře pracovního procesu tím, že služba IIS spíše než adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).

  Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Volání `SetCurrentDirectory` metody. Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.

### <a name="out-of-process-hosting-model"></a>Model hostingu mimo proces

Jak nakonfigurovat aplikaci pro hostování mimo proces, použijte jednu z následujících postupů v souboru projektu:

* Nezadávejte `<AspNetCoreHostingModel>` vlastnost. Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.
* Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnost `OutOfProcess` (hostování v procesu je nastavená pomocí `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

[Kestrel](xref:fundamentals/servers/kestrel) server se používá místo protokolu HTTP serveru služby IIS (`IISHttpServer`).

Mimo proces [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:

* Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.
* Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.

### <a name="hosting-model-changes"></a>Hostování změny modelu

Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.

Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express. Další požadavek na aplikaci vytvoří nový proces IIS Express.

### <a name="process-name"></a>Název procesu

`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.

Podporované verze Windows:

* Windows 7 nebo novější
* Windows Server 2008 R2 nebo novější

Modul funguje pouze s Kestrel. Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).

Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy. V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě. Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra. Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS). V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.

V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`. Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu. Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.

Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core. Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky. Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel. Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.

::: moniker-end

Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní. Další informace o moduly IIS aktivní s modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.

Modul ASP.NET Core můžete:

* Nastavení proměnných prostředí pro pracovní proces.
* Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.
* Předávání ověřovacích tokenů Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak nainstalovat a používat modul ASP.NET Core

Pokyny o tom, jak nainstalovat a používat modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/index>.

## <a name="configuration-with-webconfig"></a>Konfigurace pomocí souboru web.config

Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.

Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:

::: moniker range=">= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

::: moniker range=">= aspnetcore-2.2"

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`. Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.

Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributy elementu aspNetCore

::: moniker range=">= aspnetcore-2.2"

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty pro spustitelný soubor určený v **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `hostingModel` | <p>Volitelný atribut řetězce.</p><p>Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</p> | `OutOfProcess` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</p><p>&dagger;Hostování v procesu, je omezena na hodnotu `1`.</p> | Výchozí hodnota: `1`<br>Min: `1`<br>Max: `100`&dagger; |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát podle procesu **processPath** může při selhání za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p><p>Není podporováno s hostitelem v procesu.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</p><p>Neplatí pro hostování v procesu. Hostování v procesu, čeká modul app ke zpracování požadavku.</p> | Výchozí hodnota: `00:02:00`<br>Min: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</p><p>Hodnota 0 (nula) je **ne** za neomezený časový limit.</p> | Výchozí hodnota: `120`<br>Min: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni. Relativní cesty jsou relativní vzhledem k kořen webu. Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky v cestě k dispozici jsou vytvořené v modulu při vytvoření souboru protokolu. Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu. Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty pro spustitelný soubor určený v **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</p> | Výchozí hodnota: `1`<br>Min: `1`<br>Max: `100` |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát podle procesu **processPath** může při selhání za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</p> | Výchozí hodnota: `00:02:00`<br>Min: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</p><p>Hodnota 0 (nula) je **ne** za neomezený časový limit.</p> | Výchozí hodnota: `120`<br>Min: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni. Relativní cesty jsou relativní vzhledem k kořen webu. Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu. Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu. Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| Atribut | Popis | Výchozí |
| --------- | ----------- | :-----: |
| `arguments` | <p>Volitelný atribut řetězce.</p><p>Argumenty pro spustitelný soubor určený v **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Volitelný logický atribut.</p><p>Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek. Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</p> | `true` |
| `processesPerApplication` | <p>Volitelný celočíselný atribut.</p><p>Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</p> | Výchozí hodnota: `1`<br>Min: `1`<br>Max: `100` |
| `processPath` | <p>Požadovaný atribut typu string.</p><p>Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP. Jsou podporovány relativní cesty. Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</p> | |
| `rapidFailsPerMinute` | <p>Volitelný celočíselný atribut.</p><p>Určuje, kolikrát podle procesu **processPath** může při selhání za minutu. Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `100` |
| `requestTimeout` | <p>Atribut volitelný časový interval.</p><p>Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</p><p>Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.0 nebo starší `requestTimeout` musí být zadán v celých minutách, v opačném případě výchozí hodnota 2 minuty.</p> | Výchozí hodnota: `00:02:00`<br>Min: `00:00:00`<br>Max: `360:00:00` |
| `shutdownTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</p> | Výchozí hodnota: `10`<br>Min: `0`<br>Max: `600` |
| `startupTimeLimit` | <p>Volitelný celočíselný atribut.</p><p>Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu. Pokud je překročen časový limit, modul ukončí proces. Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</p> | Výchozí hodnota: `120`<br>Min: `0`<br>Max: `3600` |
| `stdoutLogEnabled` | <p>Volitelný logický atribut.</p><p>Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Volitelný atribut řetězce.</p><p>Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni. Relativní cesty jsou relativní vzhledem k kořen webu. Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty. Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu. Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu. Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a>Nastavení proměnných prostředí

::: moniker range=">= aspnetcore-3.0"

Proměnné prostředí se dá nastavit pro proces v `processPath` atribut. Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce. Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Proměnné prostředí se dá nastavit pro proces v `processPath` atribut. Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.

> [!WARNING]
> Nastavte proměnné prostředí nastavené v této části konflikt pomocí proměnných prostředí systému se stejným názvem. Pokud proměnná prostředí je nastavena v obou *web.config* souborů a systému úroveň ve Windows, hodnotu z *web.config* souboru bude připojeno k hodnotu proměnné prostředí systému (pro například `ASPNETCORE_ENVIRONMENT: Development;Development`), která zabraňuje spuštění aplikace.

::: moniker-end

Následující příklad nastaví dvou proměnných prostředí. `ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`. Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky. `CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.

::: moniker range=">= aspnetcore-2.2"

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Alternativa k nastavení prostředí přímo v *web.config* se zahrnou `<EnvironmentName>` vlastnost v profilu publikování (*.pubxml*) nebo soubor projektu. Tento přístup nastaví prostředí *web.config* při publikování projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.

## <a name="appofflinehtm"></a>app_offline.htm

Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků. Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.

Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru. Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.

::: moniker range=">= aspnetcore-2.2"

Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení. Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.

::: moniker-end

## <a name="start-up-error-page"></a>Spuštění chybovou stránku

::: moniker range=">= aspnetcore-2.2"

Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.

Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.

Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.

Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.

Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut. Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku. Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut. Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a>Vytvoření protokolu a přesměrování

Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené. Všechny složky v `stdoutLogFile` cesta vytvářejí modulem, když se vytvoří soubor protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).

Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování. Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.

Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace. Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace. Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu. Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru (*.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka. Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.

::: moniker range=">= aspnetcore-2.2"

Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB. Po spuštění se zahodí všechny další protokoly.

::: moniker-end

Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service. Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení. Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly. Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.

Úrovně (v pořadí od nejméně na nejpodrobnější):

* CHYBA
* UPOZORNĚNÍ
* INFORMACE O
* TRACE

Umístění (umístění více jsou povoleny):

* KONZOLY
* PROTOKOL UDÁLOSTÍ
* SOUBOR

Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění. (Výchozí: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.

> [!WARNING]
> Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém. Velikost souboru protokolu není omezený. Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.

::: moniker-end

Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfigurace proxy serveru používá protokol HTTP a token pro párování

::: moniker range=">= aspnetcore-2.2"

*Platí jenom pro hostování mimo proces.*

::: moniker-end

Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP. Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní. Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.

Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje. Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu. Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server. Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí. Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut. Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server. Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Modul ASP.NET Core s službu IIS sdílenou konfiguraci

Modul ASP.NET Core instalační program spustí s oprávněními **systému** účtu. Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program narazí na chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config* ve sdílené složce. Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:

1. Zakážete sdílenou konfiguraci IIS.
1. Spusťte instalační program.
1. Export aktualizovaný *applicationHost.config* souboru do sdílené složky.
1. Znovu povolte sdílenou konfiguraci IIS.

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a>Inicializace aplikací

[Inicializace aplikací služby IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce IIS, který odešle požadavek HTTP do aplikace při spuštění fondu aplikací nebo recykluje. Žádost se aktivuje spuštění aplikace. Inicializace aplikace může využívat i [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model) a [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) s modul ASP.NET Core verze 2.

Chcete-li povolit inicializaci aplikace:

1. Potvrďte, že funkce inicializace aplikace služby IIS role v povoleno:
   * Ve Windows 7 nebo novější: Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky). Otevřít **Internetová informační služba** > **webové služby** > **funkce pro vývoj aplikací**. Zaškrtněte políčko pro **inicializace aplikace**.
   * V systému Windows Server 2008 R2 nebo novější, otevřete **Průvodce přidání rolí a funkcí**. Když se dostanete **vybrat služby rolí** panelů, otevřete **vývoj aplikací** uzel a vyberte **inicializace aplikace** zaškrtávací políčko.
1. Ve Správci služby IIS vyberte **fondy aplikací** v **připojení** panelu.
1. V seznamu vyberte fond aplikací aplikaci.
1. Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.
1. Nastavte **spustit režim** k **AlwaysRunning**.
1. Otevřít **lokality** uzlu v **připojení** panelu.
1. Vyberte aplikaci.
1. Vyberte **Upřesnit nastavení** pod **spravovat web** v **akce** panelu.
1. Nastavte **předběžné načtení povoleno** k **True**.

Další informace najdete v tématu [inicializace aplikace služby IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).

Aplikace, které používají [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) musíte použít externí služby pravidelně k udržení příkazem ping otestovat aplikaci.

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Verze modulu a hostování sady Instalační protokoly

Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:

1. V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.
1. Vyhledejte *aspnetcore.dll* souboru.
1. Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.
1. Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.

Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Modul, schéma a konfiguraci umístění souborů

### <a name="module"></a>Modul

**IIS (x86/amd64):**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll

   * % ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

**Služba IIS Express (x86/amd64):**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

   * % ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

### <a name="schema"></a>Schéma

**SLUŽBA IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML

::: moniker-end
**Služba IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

::: moniker-end

### <a name="configuration"></a>Konfigurace

**SLUŽBA IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**Služba IIS Express**

   * Visual Studio: {Kořenový adresář aplikace}\\.vs\config\applicationHost.config
   
   * *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* [Úložiště GitHub modulu jádra ASP.NET (odkaz na zdroj)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
