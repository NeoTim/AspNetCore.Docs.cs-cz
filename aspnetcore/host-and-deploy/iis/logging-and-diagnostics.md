---
title: Vytvoření a přesměrování protokolu pomocí modulu ASP.NET Core
author: rick-anderson
description: Nakonfigurujte službu IIS a modul ASP.NET Core pro zaznamenávání protokolů a diagnostických informací.
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 523eec53d7d21723dcf136c4e5ce299533a78cc6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91901022"
---
# <a name="log-creation-and-redirection"></a>Vytvoření a přesměrování protokolu

Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu. `stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).

Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu. Je zodpovědností hostitele omezit místo na disku, které používají protokoly.

Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.

Nepoužívejte protokol stdout pro obecné účely protokolování aplikace. Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly. Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).

Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu. Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( `.log` ) k poslednímu segmentu `stdoutLogFile` cesty (obvykle `stdout` ) oddělené podtržítky. Pokud `stdoutLogFile` cesta končí `stdout` , protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru `stdout_20180205194132_1934.log` .

Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB. Po spuštění budou všechny další protokoly zahozeny.

Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` . Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` . `%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.

Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.

Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Rozšířené diagnostické protokoly

Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly. Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v `web.config` . Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:

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

Všechny složky v cestě ( `logs` v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu. Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).

Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.

Úrovně (v pořadí od nejméně po nejpodrobnější):

* CHYBA
* WARNING
* PŘÍJEMCE
* TRACE

Umístění (je povoleno více umístění):

* STROMU
* EVENTLOG
* SOUBOR

Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění. (Výchozí: `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.

> [!WARNING]
> Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému. Velikost protokolu není omezená. Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.

Příklad prvku v souboru naleznete v tématu [Konfigurace modulu `web.config` ASP.NET Core](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) `aspNetCore` `web.config` .
