---
title: Obecný hostitel rozhraní .NET
author: rick-anderson
description: Další informace o obecném hostiteli .NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417635"
---
# <a name="net-generic-host"></a>Obecný hostitel rozhraní .NET

::: moniker range=">= aspnetcore-5.0"

Tento článek představuje obecný hostitel .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny, jak jej používat.

## <a name="whats-a-host"></a>Co je to hostitel?

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:

* Vkládání závislostí (DI)
* Protokolování
* Konfigurace
* `IHostedService`Implementace

Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI. Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.

Ve verzích ASP.NET Core starší než 3.0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy HTTP. Webový hostitel se již nedoporučuje pro webové aplikace a zůstává k dispozici pouze pro zpětnou kompatibilitu.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě. Metoda: `Main`

* Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.
* Volání `Build` `Run` a metody objektu tvůrce.

Tady je *Program.cs* kód pro úlohy bez `IHostedService` http, s jedinou implementací přidanou do kontejneru DI. 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace. Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce

Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s `DOTNET_`předponou .
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appsettings.json*.
  * *nastavení aplikace. {Prostředí}.json*.
  * [Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)
  * Konzola
  * Ladit
  * EventSource
  * EventLog (pouze při spuštění v systému Windows)
* Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.

Metoda: `ConfigureWebHostDefaults`

* Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .
* Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .
* Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`
* Umožňuje integraci iis. Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Rámcové služby

Následující služby jsou registrovány automaticky:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace. Rozhraní také obsahuje `StopApplication` metodu.

Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení. Používá se poslední registrovaná implementace.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace. `ConsoleLifetime`:

* Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.
* Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostŽivotní prostředí

Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:

* [Název_aplikace](#applicationname)
* [Název prostředí](#environmentname)
* [ContentRootPath](#contentrootpath)

Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.

Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř . Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.

Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder` `ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky. Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.

Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do . Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou. Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikací

Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na . `ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky. Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč. 

Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http. Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.

**Klíč**:`applicationName`  
**Typ**:`string`  
**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.  
**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí. 

### <a name="contentrootpath"></a>ContentRootPath

Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitel se nepodaří spustit.

**Klíč**:`contentRoot`  
**Typ**:`string`  
**Výchozí**: Složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace naleznete v tématu:

* [Základy: Kořen obsahu](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Název prostředí

Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a . Hodnoty nerozlišují malá a velká písmena.

**Klíč**:`environment`  
**Typ**:`string`  
**Výchozí :**`Production`  
**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>Timeout vypnutí

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro . Výchozí hodnota je pět sekund.  Během časového období hostitel:

* Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.

Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace. Služby se zastaví, i když ještě nedokončily zpracování. Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.

**Klíč**:`shutdownTimeoutSeconds`  
**Typ**:`int`  
**Výchozí**: 5 sekund  
**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte . Následující příklad nastaví časový čas na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Zakázat opětovné načtení konfigurace aplikace při změně

[Ve výchozím nastavení](xref:fundamentals/configuration/index#default), *appsettings.json* a *appsettings.{ Prostředí}.json* jsou znovu načteny při změně souboru. Chcete-li zakázat toto chování opětovného načtení v `hostBuilder:reloadConfigOnChange` ASP.NET `false`Core 5.0 Preview 3 nebo novější, nastavte klíč na .

**Klíč**:`hostBuilder:reloadConfigOnChange`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`true`  
**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`  
**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> Oddělovač`:`dvojtečky ( ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách. Další informace naleznete v tématu [Proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).

## <a name="settings-for-web-apps"></a>Nastavení webových aplikací

Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP. Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.

Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření. Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>Chyby po spuštění systému Capture

Když `false`, chyby při spuštění za následek ukončení hostitele. Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.

**Klíč**:`captureStartupErrors`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .  
**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Podrobné chyby

Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.

**Klíč**:`detailedErrors`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`false`  
**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>Hostovánístartupových sestavení

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění. Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace. Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.

**Klíč**:`hostingStartupAssemblies`  
**Typ**:`string`  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostováníStartupExcludeAssemblies

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.

**Klíč**:`hostingStartupExcludeAssemblies`  
**Typ**:`string`  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).

**Klíč**:`https_port`  
**Typ**:`string`  
**Výchozí**: Výchozí hodnota není nastavena.  
**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferhostingUrls

Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.

**Klíč**:`preferHostingUrls`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`true`  
**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`

Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**:`preventHostingStartup`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`false`  
**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení k hledání `Startup` třídy.

**Klíč**:`startupAssembly`  
**Typ**:`string`  
**Výchozí**: Sestavení aplikace  
**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání . `UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ). Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům. Například, `http://localhost:123`. Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ). Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**:`urls`  
**Typ**:`string`  
**Výchozí** `http://localhost:5000` : a`https://localhost:5001`  
**Proměnná prostředí**:`<PREFIX_>URLS`

Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má vlastní konfigurační rozhraní API koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

Relativní cesta ke statickým prostředkům aplikace.

**Klíč**:`webroot`  
**Typ**:`string`  
**Výchozí**: Výchozí `wwwroot`hodnota je . Cesta k *kořenovému adresáři obsahu}/www root* musí existovat. Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.  
**Proměnná prostředí**:`<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, `UseWebRoot`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseWebRoot("public");
```

Další informace naleznete v tématu:

* [Základy: Kořen webu](xref:fundamentals/index#web-root)
* [ContentRootPath](#contentrootpath)

## <a name="manage-the-host-lifetime"></a>Správa doby trvání hostitele

Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci. Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spusťte

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.

### <a name="runasync"></a>Synchronizace runasync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.

### <a name="startasync"></a>Spuštění synchronizace

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním. To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.

### <a name="waitforshutdown"></a>Čekání na vypnutí

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>Čekánínavypnutíasynchroniku

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .

### <a name="external-control"></a>Externí ovládání

Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

Tento článek představuje obecný hostitel .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny, jak jej používat.

## <a name="whats-a-host"></a>Co je to hostitel?

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, například:

* Vkládání závislostí (DI)
* Protokolování
* Konfigurace
* `IHostedService`Implementace

Při spuštění hostitele volá `IHostedService.StartAsync` na každou <xref:Microsoft.Extensions.Hosting.IHostedService> implementaci, která najde v kontejneru DI. Ve webové aplikaci je `IHostedService` jednou z implementací webová služba, která spouští [implementaci http serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.

Ve verzích ASP.NET Core starší než 3.0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy HTTP. Webový hostitel se již nedoporučuje pro webové aplikace a zůstává k dispozici pouze pro zpětnou kompatibilitu.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurován, sestaven a spuštěn `Program` podle kódu ve třídě. Metoda: `Main`

* Volá `CreateHostBuilder` metodu k vytvoření a konfiguraci objektu tvůrce.
* Volání `Build` `Run` a metody objektu tvůrce.

Tady je *Program.cs* kód pro úlohy bez `IHostedService` http, s jedinou implementací přidanou do kontejneru DI. 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Pro úlohu HTTP `Main` je metoda `CreateHostBuilder` stejná, ale volá `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace používá Core entity frameworku, neměňte `CreateHostBuilder` název nebo podpis metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že najdou metodu, `CreateHostBuilder` která konfiguruje hostitele bez spuštění aplikace. Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce

Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>cestu vrácenou programem .
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s `DOTNET_`předponou .
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appsettings.json*.
  * *nastavení aplikace. {Prostředí}.json*.
  * [Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování:](xref:fundamentals/logging/index)
  * Konzola
  * Ladit
  * EventSource
  * EventLog (pouze při spuštění v systému Windows)
* Umožňuje [ověření oboru](xref:fundamentals/dependency-injection#scope-validation) a ověření [závislostí,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) když je prostředí Vývoj.

Metoda: `ConfigureWebHostDefaults`

* Načte konfiguraci hostitele z `ASPNETCORE_`proměnných prostředí s předponou .
* Nastaví [server Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje jej pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .
* Přidá [middleware filtrování hostitelů](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware s předaných záhlaví,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) pokud `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se rovná . `true`
* Umožňuje integraci iis. Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a nastavení pro webové [aplikace](#settings-for-web-apps) části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Rámcové služby

Následující služby jsou registrovány automaticky:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných <xref:fundamentals/dependency-injection#framework-provided-services>rámcem naleznete v tématu .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Vstříkněte `IApplicationLifetime`(dříve) službu do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci metod spuštění aplikace a zastavení aplikace. Rozhraní také obsahuje `StopApplication` metodu.

Následující příklad je `IHostedService` implementace, `IHostApplicationLifetime` která registruje události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> řídí při spuštění hostitele a při jeho zastavení. Používá se poslední registrovaná implementace.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementace. `ConsoleLifetime`:

* Naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí.
* Odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostŽivotní prostředí

Vložte <xref:Microsoft.Extensions.Hosting.IHostEnvironment> službu do třídy, abyste získali informace o následujících nastaveních:

* [Název_aplikace](#applicationname)
* [Název prostředí](#environmentname)
* [ContentRootPath](#contentrootpath)

Webové aplikace `IWebHostEnvironment` implementují rozhraní, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro <xref:Microsoft.Extensions.Hosting.IHostEnvironment> vlastnosti implementace.

Konfigurace hostitele je k dispozici z <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř . Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , je nahrazen a konfigurátor aplikace.

Chcete-li přidat <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> konfiguraci hostitele, volejte na . `IHostBuilder` `ConfigureHostConfiguration`lze volat vícekrát s aditivními výsledky. Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.

Zprostředkovatel proměnných prostředí `DOTNET_` s argumenty předpony a `CreateDefaultBuilder`příkazového řádku jsou zahrnuty do . Pro webové aplikace je přidán poskytovatel `ASPNETCORE_` proměnných prostředí s předponou. Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí `ASPNETCORE_ENVIRONMENT` pro se stane `environment` hodnotou konfigurace hostitele pro klíč.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikací

Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří `IHostBuilder`voláním na . `ConfigureAppConfiguration`lze volat vícekrát s aditivními výsledky. Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč. 

Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako služba z DI. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedena nastavení hostitele, která platí pro úlohy protokolu HTTP i jiné než http. Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele.

**Klíč**:`applicationName`  
**Typ**:`string`  
**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.  
**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí. 

### <a name="contentrootpath"></a>ContentRootPath

Vlastnost [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitel se nepodaří spustit.

**Klíč**:`contentRoot`  
**Typ**:`string`  
**Výchozí**: Složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, `UseContentRoot` `IHostBuilder`použijte proměnnou prostředí nebo volejte :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace naleznete v tématu:

* [Základy: Kořen obsahu](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Název prostředí

Vlastnost [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a . Hodnoty nerozlišují malá a velká písmena.

**Klíč**:`environment`  
**Typ**:`string`  
**Výchozí :**`Production`  
**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, `UseEnvironment` `IHostBuilder`použijte proměnnou prostředí nebo volejte :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>Timeout vypnutí

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>výtok pro . Výchozí hodnota je pět sekund.  Během časového období hostitel:

* Aktivuje [iHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Pokusí se zastavit hostované služby, protokolování chyb pro služby, které se nepodaří zastavit.

Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace. Služby se zastaví, i když ještě nedokončily zpracování. Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.

**Klíč**:`shutdownTimeoutSeconds`  
**Typ**:`int`  
**Výchozí**: 5 sekund  
**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Chcete-li tuto hodnotu nastavit, `HostOptions`použijte proměnnou prostředí nebo nakonfigurujte . Následující příklad nastaví časový čas na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Nastavení webových aplikací

Některá nastavení hostitele platí pouze pro úlohy PROTOKOLU HTTP. Ve výchozím nastavení mohou mít proměnné prostředí `DOTNET_` používané `ASPNETCORE_` ke konfiguraci těchto nastavení předponu nebo ji.

Pro tato `IWebHostBuilder` nastavení jsou k dispozici metody rozšíření. Ukázky kódu, které ukazují, `webBuilder` jak volat `IWebHostBuilder`metody rozšíření assume je instance , jako v následujícím příkladu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>Chyby po spuštění systému Capture

Když `false`, chyby při spuštění za následek ukončení hostitele. Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.

**Klíč**:`captureStartupErrors`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .  
**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Podrobné chyby

Pokud je povoleno nebo `Development`pokud je prostředí , aplikace zachycuje podrobné chyby.

**Klíč**:`detailedErrors`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`false`  
**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>Hostovánístartupových sestavení

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění. Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace. Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.

**Klíč**:`hostingStartupAssemblies`  
**Typ**:`string`  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostováníStartupExcludeAssemblies

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.

**Klíč**:`hostingStartupExcludeAssemblies`  
**Typ**:`string`  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).

**Klíč**:`https_port`  
**Typ**:`string`  
**Výchozí**: Výchozí hodnota není nastavena.  
**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferhostingUrls

Označuje, zda má hostitel naslouchat adresám `IWebHostBuilder` URL nakonfigurovaným s `IServer` namísto adres URL nakonfigurovaných s implementací.

**Klíč**:`preferHostingUrls`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`true`  
**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`

Chcete-li nastavit tuto hodnotu, `PreferHostingUrls`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**:`preventHostingStartup`  
**Typ** `bool` :`true` `1`( ( nebo )  
**Výchozí :**`false`  
**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Chcete-li nastavit tuto hodnotu, `UseSetting` použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení k hledání `Startup` třídy.

**Klíč**:`startupAssembly`  
**Typ**:`string`  
**Výchozí**: Sestavení aplikace  
**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, `UseStartup`použijte proměnnou prostředí nebo volání . `UseStartup`může mít název`string`sestavení ( )`TStartup`nebo typ ( ). Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Seznam adres IP oddělených středníkem nebo hostitelských adres s porty a protokoly, které by měl server naslouchat požadavkům. Například, `http://localhost:123`. Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ). Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**:`urls`  
**Typ**:`string`  
**Výchozí** `http://localhost:5000` : a`https://localhost:5001`  
**Proměnná prostředí**:`<PREFIX_>URLS`

Chcete-li nastavit tuto hodnotu, `UseUrls`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má vlastní konfigurační rozhraní API koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

Relativní cesta ke statickým prostředkům aplikace.

**Klíč**:`webroot`  
**Typ**:`string`  
**Výchozí**: Výchozí `wwwroot`hodnota je . Cesta k *kořenovému adresáři obsahu}/www root* musí existovat. Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.  
**Proměnná prostředí**:`<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, `UseWebRoot`použijte proměnnou prostředí nebo volání :

```csharp
webBuilder.UseWebRoot("public");
```

Další informace naleznete v tématu:

* [Základy: Kořen webu](xref:fundamentals/index#web-root)
* [ContentRootPath](#contentrootpath)

## <a name="manage-the-host-lifetime"></a>Správa doby trvání hostitele

Volání metody na <xref:Microsoft.Extensions.Hosting.IHost> vytvořené implementace spustit a zastavit aplikaci. Tyto metody <xref:Microsoft.Extensions.Hosting.IHostedService> ovlivňují všechny implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spusťte

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne.

### <a name="runasync"></a>Synchronizace runasync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.

### <a name="startasync"></a>Spuštění synchronizace

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a <xref:System.Threading.Tasks.Task> vrátí, který dokončí po aktivaci tokenu zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na `StartAsync`začátku , který čeká, až bude dokončena před pokračováním. To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.

### <a name="waitforshutdown"></a>Čekání na vypnutí

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud vypnutí je spuštěna IHostLifetime, například přes <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>Čekánínavypnutíasynchroniku

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .

### <a name="external-control"></a>Externí ovládání

Příméřízení životnosti hostitele lze dosáhnout pomocí metod, které lze volat externě:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET aplikace Core nakonfigurují a spouštějí hostitele. Hostitel je zodpovědný za spuštění aplikace a správu životnosti.

Tento článek popisuje ASP.NET základní<xref:Microsoft.Extensions.Hosting.HostBuilder>obecný hostitel ( ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.

Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele a povolit tak širší škálu hostitelských scénářů. Zasílání zpráv, úlohy na pozadí a další úlohy bez protokolu HTTP založené na obecném hostiteli těží z průřezových funkcí, jako je konfigurace, vkládání závislostí (DI) a protokolování.

Obecný hostitel je nový v ASP.NET Core 2.1 a není vhodný pro scénáře webhostingu. Pro scénáře webhostingu použijte [webový hostitel](xref:fundamentals/host/web-host). Obecný hostitel nahradí webhostingu v budoucí verzi a bude fungovat jako primární rozhraní API hostitele ve scénářích HTTP i bez protokolu HTTP.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Při spuštění ukázkové aplikace v [kódu Visual Studia](https://code.visualstudio.com/)použijte externí nebo integrovaný *terminál*. Nespouštějte ukázku `internalConsole`v .

Nastavení konzoly v kódu sady Visual Studio:

1. Otevřete soubor *.vscode/launch.json.*
1. V konfiguraci **spuštění jádra .NET (konzola)** vyhledejte položku **konzoly.** Nastavte hodnotu `externalTerminal` na `integratedTerminal`buď nebo .

## <a name="introduction"></a>Úvod

Knihovna obecného hostitele <xref:Microsoft.Extensions.Hosting> je k dispozici v oboru názvů a je k dispozici v balíčku [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) Balíček `Microsoft.Extensions.Hosting` je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).

<xref:Microsoft.Extensions.Hosting.IHostedService>je vstupní bod pro spuštění kódu. Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace je spuštěna v pořadí [registrace služby v ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána <xref:Microsoft.Extensions.Hosting.IHostedService> na každý při <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> spuštění hostitele a je volána v pořadí reverzní registrace při hostování řádně vypne.

## <a name="set-up-a-host"></a>Nastavení hostitele

<xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní komponenta, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Možnosti

<xref:Microsoft.Extensions.Hosting.HostOptions>konfigurovat možnosti <xref:Microsoft.Extensions.Hosting.IHost>pro rozhraní .

### <a name="shutdown-timeout"></a>Časový čas vypnutí

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>nastaví časový <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>čas pro . Výchozí hodnota je pět sekund.

Následující konfigurace možností `Program.Main` zvyšuje výchozí pětisekundový časový limit vypnutí na 20 sekund:

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Výchozí služby

Následující služby jsou registrovány během inicializace hostitele:

* [Životní](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>prostředí ( )
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfigurace](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele je vytvořena:

* Volání metody <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření na nastavení [kořenového obsahu](#content-root) a [prostředí](#environment).
* Čtení konfigurace od <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>zprostředkovatelů konfigurace v .

### <a name="extension-methods"></a>Metody rozšíření

### <a name="application-key-name"></a>Aplikační klíč (název)

Vlastnost [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena z konfigurace hostitele během výstavby hostitele. Chcete-li explicitně nastavit hodnotu, použijte [klíč HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klíč**:`applicationName`  
**Typ**:`string`  
**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.  
**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Proměnná** `<PREFIX_>APPLICATIONNAME` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

### <a name="content-root"></a>Kořen obsahu

Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.

**Klíč**:`contentRoot`  
**Typ**:`string`  
**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.  
**Nastavit pomocí**:`UseContentRoot`  
**Proměnná** `<PREFIX_>CONTENTROOT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Pokud cesta neexistuje, hostitel se nepodaří spustit.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Další informace naleznete [v tématu Základy: Kořen obsahu](xref:fundamentals/index#content-root).

### <a name="environment"></a>Prostředí

Nastaví [prostředí](xref:fundamentals/environments)aplikace .

**Klíč**:`environment`  
**Typ**:`string`  
**Výchozí :**`Production`  
**Nastavit pomocí**:`UseEnvironment`  
**Proměnná** `<PREFIX_>ENVIRONMENT` prostředí`<PREFIX_>` : ( je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Prostředí lze nastavit na libovolnou hodnotu. Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a . Hodnoty nerozlišují malá a velká písmena.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>Konfiguracekonfigurace host

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření hostitele. Konfigurace hostitele se používá k <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicializaci pro použití v procesu sestavení aplikace.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>lze volat vícekrát s aditivními výsledky. Hostitel používá podle toho, která možnost nastaví hodnotu poslední na daný klíč.

Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé. Je nutné explicitně zadat, bez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ohledu na to, jaké zprostředkovatele konfigurace aplikace vyžaduje v aplikaci , včetně:

* Konfigurace souboru (například ze souboru *hostsettings.json).*
* Konfigurace proměnných prostředí.
* Konfigurace argumentu příkazového řádku.
* Jakékoli další požadované zprostředkovatele konfigurace.

Konfigurace souboru hostitele je povolena zadáním základní `SetBasePath` cesty aplikace s následovaným voláním jednoho z [poskytovatelů konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider). Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat nastavení konfigurace hostitele souboru.

Chcete-li přidat [konfiguraci proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> tvůrce hostitele. `AddEnvironmentVariables`přijme volitelnou uživatelsky definovanou předponu. Ukázková aplikace používá předponu `PREFIX_`. Předpona je odebrána při čtení proměnných prostředí. Když je nakonfigurován hostitel ukázkové aplikace, `PREFIX_ENVIRONMENT` hodnota proměnné prostředí pro `environment` se stane hodnotou konfigurace hostitele pro klíč.

Během vývoje při použití sady Visual `dotnet run` [Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s aplikací mohou být proměnné prostředí nastaveny v souboru *Vlastnosti/spuštěníNastavení.json.* V [kódu sady Visual Studio](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *.vscode/launch.json* během vývoje. Další informace naleznete v tématu <xref:fundamentals/environments>.

[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>přidána voláním . Konfigurace příkazového řádku je přidána jako poslední, aby bylo možné přepsat konfiguraci poskytovanou staršími poskytovateli konfigurace.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Další konfiguraci lze poskytnout s [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.

Příklad `HostBuilder` konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>pomocí :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>Konfigurace konfigurace

Konfigurace aplikace se <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> vytvoří <xref:Microsoft.Extensions.Hosting.IHostBuilder> voláním na implementaci. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> an k <xref:Microsoft.Extensions.Configuration.IConfiguration> vytvoření aplikace. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>lze volat vícekrát s aditivními výsledky. Aplikace používá podle toho, která možnost nastaví hodnotu jako poslední na daný klíč. Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uživatelem je k dispozici na adrese <xref:Microsoft.Extensions.Hosting.IHost.Services*> [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v aplikaci .

Konfigurace aplikace automaticky přijímá konfiguraci hostitele poskytovanou [společností ConfigureHostConfiguration](#configurehostconfiguration).

Příklad konfigurace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>aplikace pomocí :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*nastavení aplikace. Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*nastavení aplikace. Production.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu. Ukázková aplikace přesune soubory nastavení aplikace JSON a *hostsettings.json* s následující `<Content>` položkou:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozšíření konfigurace, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> jako jsou například a vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Pokud aplikace nepoužívá [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), musí být tyto balíčky přidány do projektu kromě základního balíčku [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>lze volat vícekrát s aditivními výsledky.

Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` používá metodu rozšíření k přidání `LifetimeEventsHostedService`služby pro události `TimedHostedService`životnosti a časované úlohy na pozadí , do aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>Konfigurovat protokolování

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>přidá delegáta pro konfiguraci <xref:Microsoft.Extensions.Logging.ILoggingBuilder>poskytovaného . <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>mohou být volány vícekrát s aditivními výsledky.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> SIGTERM a volá ke spuštění procesu vypnutí. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření, jako je [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync). `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předběžně zaregistrována jako výchozí implementace životnosti. Používá se poslední registrovaná životnost.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfigurace kontejneru

Pro podporu připojení jiných kontejnerů může <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>hostitel přijmout soubor . Poskytování factory není součástí registrace kontejneru DI, ale je hostitele vnitřní slouží k vytvoření kontejneru betonové DI. [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu použitou k vytvoření poskytovatele služeb aplikace.

Vlastní konfigurace kontejneru <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> je spravována metodou. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje prostředí silného typu pro konfiguraci kontejneru nad základní rozhraní API hostitele. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>lze volat vícekrát s aditivními výsledky.

Vytvoření kontejneru služeb pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Poskytněte továrnu na servisní kontejnery:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Použijte továrnu a nakonfigurujte vlastní kontejner služeb pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost hostitele se provádí pomocí <xref:Microsoft.Extensions.Hosting.IHostBuilder>rozšiřujících metod na . Následující příklad ukazuje, jak metoda <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozšíření rozšiřuje implementaci s [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad demonstroval v <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikace vytvoří metodu `UseHostedService` rozšíření pro registraci hostované služby předané v `T`:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Správa hostitele

Implementace <xref:Microsoft.Extensions.Hosting.IHost> je zodpovědná za spuštění <xref:Microsoft.Extensions.Hosting.IHostedService> a zastavení implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spusťte

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>Synchronizace runasync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a <xref:System.Threading.Tasks.Task> vrátí, která se dokončí, když se aktivuje token zrušení nebo vypnutí:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>povolí podporu konzoly, vytvoří a spustí hostitele a čeká na vypnutí <kbd>kláves Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Spustit a zastavitsynchronizacu

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí hostitele synchronně.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusí se zastavit hostitele v rámci poskytnutého časového času.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync a StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí aplikaci.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zastaví aplikaci.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>Čekání na vypnutí

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>se spouští <xref:Microsoft.Extensions.Hosting.IHostLifetime>přes `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` , například (naslouchá <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT nebo SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>Čekánínavypnutíasynchroniku

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>vrátí, <xref:System.Threading.Tasks.Task> který dokončí při vypnutí je spuštěna <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>prostřednictvím daného tokenu a volání .

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Externí ovládání

Externího řízení hostitele lze dosáhnout pomocí metod, které lze volat externě:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>začátku , který čeká, až bude dokončena před pokračováním. To lze použít ke zpoždění spuštění, dokud signalizováno externí událostí.

## <a name="ihostingenvironment-interface"></a>Rozhraní IHostingEnvironment

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace. Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Rozhraní IApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádného požadavku na vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.

| Token zrušení | Aktivuje se při&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Hostitel byl plně spuštěn. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Hostitel dokončuje řádné vypnutí. Všechny požadavky by měly být zpracovány. Vypnutí blokuje, dokud tato událost nedokončí. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Hostitel provádí řádné vypnutí. Požadavky mohou být stále zpracování. Vypnutí blokuje, dokud tato událost nedokončí. |

Konstruktor vstříkne službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání `LifetimeEventsHostedService` konstruktoru <xref:Microsoft.Extensions.Hosting.IHostedService> do třídy (implementace) k registraci událostí.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace. Následující třída <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/host/hosted-services>
