---
title: Obecný hostitel .NET
author: tdykstra
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 1582955cd18e6739111af05c9a892cd5cb4e270d
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007240"
---
# <a name="net-generic-host"></a>Obecný hostitel .NET

::: moniker range=">= aspnetcore-3.0"

Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.

## <a name="whats-a-host"></a>Co je hostitel?

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:

* Vkládání závislostí (DI)
* Protokolování
* Konfiguraci
* @no__t – 0 implementace

Při spuštění hostitele zavolá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou najde v kontejneru DI. V rámci webové aplikace je jednou z implementací `IHostedService` webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.

Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http. Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`. `Main` Metody:

* Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.
* Zavolá @no__t metody-0 a `Run` v objektu Tvůrce.

Zde je *program.cs* kód pro úlohu jiného typu než HTTP a jedna implementace `IHostedService` přidána do kontejneru di. 

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

Pro úlohy HTTP je metoda `Main` stejná, ale volání `CreateHostBuilder` `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu metody `CreateHostBuilder`. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny metody `CreateHostBuilder`, které nakonfigurují hostitele bez spuštění aplikace. Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce 

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:

* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s předponou "DOTNET_".
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží v prostředí `Development`.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :
  * Konzola
  * Ladění
  * EventSource
  * Protokol událostí (pouze při spuštění v systému Windows)
* Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.

`ConfigureWebHostDefaults` Metody:

* Načte konfiguraci hostitele z proměnných prostředí s předponou "ASPNETCORE_".
* Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.
* Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.
* Povolí integraci služby IIS. Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Služby poskytované frameworkem

Mezi služby, které jsou zaregistrovány automaticky, patří následující:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Vložením služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy můžete zpracovat úlohy po spuštění a řádné vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace. Rozhraní obsahuje také metodu `StopApplication`.

Následující příklad je implementace `IHostedService` registrující události `IHostApplicationLifetime`:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví. Použije se Poslední registrovaná implementace.

<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je výchozí implementace `IHostLifetime`. `ConsoleLifetime`:

* čeká na klávesovou zkratku CTRL + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.
* Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujícím:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentrootpath)

Webové aplikace implementují rozhraní @no__t 0, které dědí `IHostEnvironment` a přidává:

* [WebRootPath](#webroot)

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. Po `ConfigureAppConfiguration` se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.

Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`. `ConfigureHostConfiguration` se dá volat víckrát s přičítáním výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou obsaženy v CreateDefaultBuilder. U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`. Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro klíč `environment`.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikace

Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v `IHostBuilder`. `ConfigureAppConfiguration` se dá volat víckrát s přičítáním výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. 

Konfigurace vytvořená `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.

**Klíč**: ApplicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace.
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`

K nastavení této hodnoty použijte proměnnou prostředí. 

### <a name="contentrootpath"></a>ContentRootPath

Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitele se nepodaří spustit.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Složka, ve které se nachází sestavení aplikace  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseContentRoot` v `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace naleznete v tématu:

* @no__t – 0Fundamentals: Kořen obsahu @ no__t-0
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Mezi hodnoty definované rozhraním patří `Development`, `Staging` a `Production`. U hodnot se nerozlišují malá a velká písmena.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: Produkční  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseEnvironment` v `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.  Po uplynutí časového limitu hostitel:

* Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná. Služby se zastavují i v případě, že se nedokončily zpracování. Pokud služby vyžadují ukončení déle, zvyšte časový limit.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: 5 sekund **Proměnná prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`. Následující příklad nastaví časový limit na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Nastavení pro Web Apps

Některá nastavení hostitele se vztahují jenom na úlohy HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.

Pro tato nastavení jsou k dispozici metody rozšíření `IWebHostBuilder`. Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Pokud `false`, chyby během spuštění hostitele se ukončí. Při `true` zachytí hostitel při spuštění výjimky a pokusí se spustit server.

**Klíč**: captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: Výchozí hodnota je `false`, pokud se aplikace nespustí s Kestrel za IIS, kde výchozí hodnota je `true`.  
**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění. I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace. Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.

**Klíč**: hostingStartupExcludeAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování https](xref:security/enforcing-ssl).

**Klíč**: https_port  
**Typ**: *řetězec*  
**Výchozí**: Výchozí hodnota není nastavena.  
**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte metodu Configuration nebo call `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Určuje, zda má hostitel naslouchat adresám URL konfigurovaným pomocí `IWebHostBuilder` namísto nastavení nakonfigurovaného pomocí implementace `IServer`.

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `PreferHostingUrls`:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**: preventHostingStartup  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení, ve kterém se má hledat třída `Startup`

**Klíč**: startupAssembly  
**Typ**: *řetězec*  
**Výchozí**: Sestavení aplikace  
**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`. `UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`). Je-li voláno více metod `UseStartup`, má poslední přednost.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL – adresy

Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky. Například, `http://localhost:123`. Pomocí "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`). Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**: adresy URL  
**Typ**: *řetězec*  
**Výchozí**: `http://localhost:5000` a `https://localhost:5001`  
**Proměnná prostředí**: `<PREFIX_>URLS`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseUrls`:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Relativní cesta ke statickým prostředkům aplikace

**Klíč**: Webroot  
**Typ**: *řetězec*  
**Výchozí**: Výchozí hodnota je `wwwroot`. Cesta k *obsahu {root}/wwwroot* musí existovat. Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.  
**Proměnná prostředí**: `<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseWebRoot`:

```csharp
webBuilder.UseWebRoot("public");
```

Další informace naleznete v tématu:

* @no__t – 0Fundamentals: Webový kořenový adresář @ no__t-0
* [ContentRootPath](#contentrootpath)

## <a name="manage-the-host-lifetime"></a>Správa životnosti hostitele

Pro spuštění a zastavení aplikace volejte metody pro vytvořenou implementaci <xref:Microsoft.Extensions.Hosting.IHost>. Tyto metody ovlivňují všechny implementace <xref:Microsoft.Extensions.Hosting.IHostedService>, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spusťte

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> povolí podporu konzoly, sestaví a spustí hostitele a počká, až se vypne CTRL + C/SIGINT nebo SIGTERM.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například prostřednictvím CTRL + C/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Externí ovládací prvek

Přímou kontrolu životnosti hostitelů lze dosáhnout pomocí metod, které mohou být volány externě:

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

ASP.NET Core aplikace nakonfigurují a spouštějí hostitele. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.

Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.

Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů. Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.

Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů. U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host). Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))

Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*. Nespouštějte ukázku v `internalConsole`.

Nastavení konzoly v Visual Studio Code:

1. Otevřete soubor *. VSCode/Launch. JSON* .
1. V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** . Nastavte hodnotu na buď `externalTerminal`, nebo `integratedTerminal`.

## <a name="introduction"></a>Úvod

Knihovna obecných hostitelů je k dispozici v oboru názvů <xref:Microsoft.Extensions.Hosting> a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) . Balíček `Microsoft.Extensions.Hosting` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).

<xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu. Každá implementace <xref:Microsoft.Extensions.Hosting.IHostedService> se spouští v pořadí [Registrace služby v ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> se volá na každé <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a v pořadí zpětné registrace se volá <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>, když se hostitel řádně ukončí.

## <a name="set-up-a-host"></a>Nastavení hostitele

<xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součást, kterou knihovny a aplikace používají k inicializaci, sestavení a spuštění hostitele:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Možnosti

<xref:Microsoft.Extensions.Hosting.HostOptions> konfigurovat možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Časový limit vypnutí

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.

Následující konfigurace možnosti v `Program.Main` zvyšuje výchozí časový limit pět sekund na 20 sekund:

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

Během inicializace hostitele jsou registrovány následující služby:

* [Prostředí](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfigurace](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konfigurace hostitele

Konfiguraci hostitele vytvořil:

* Volání metod rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> pro nastavení [kořenového adresáře obsahu](#content-root) a [prostředí](#environment).
* Čtení konfigurace od zprostředkovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.

### <a name="extension-methods"></a>Metody rozšíření

### <a name="application-key-name"></a>Klíč aplikace (název)

Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele. Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klíč**: ApplicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace  
**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

### <a name="content-root"></a>Kořenový adresář obsahu

Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.  
**Nastavit pomocí**: `UseContentRoot`  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Pokud cesta neexistuje, hostitele se nepodaří spustit.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Další informace najdete v tématu [Fundamentals: Kořen obsahu @ no__t-0.

### <a name="environment"></a>Prostředí

Nastaví [prostředí](xref:fundamentals/environments)aplikace.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: Produkční  
**Nastavit pomocí**: `UseEnvironment`  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Prostředí lze nastavit na libovolnou hodnotu. Mezi hodnoty definované rozhraním patří `Development`, `Staging` a `Production`. U hodnot se nerozlišují malá a velká písmena.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele. Konfigurace hostitele se používá k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> se dá volat víckrát s přičítáním výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé. Musíte explicitně zadat, co vyžadují poskytovatelé konfigurace, aby aplikace <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:

* Konfigurace souboru (například ze souboru *HostSettings. JSON* ).
* Konfigurace proměnné prostředí.
* Konfigurace argumentů příkazového řádku.
* Všichni další povinní poskytovatelé konfigurace.

Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace s `SetBasePath` následovaným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider). Ukázková aplikace používá soubor JSON *HostSettings. JSON*a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> pro využívání nastavení konfigurace hostitele souboru.

Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrci hostitele. `AddEnvironmentVariables` akceptuje volitelnou uživatelem definovanou předponu. Ukázková aplikace používá předponu `PREFIX_`. Předpona je odebrána při čtení proměnných prostředí. Po nakonfigurování hostitele ukázkové aplikace se hodnota proměnné prostředí `PREFIX_ENVIRONMENT` stala hodnotou konfigurace hostitele pro klíč `environment`.

Během vývoje při použití sady [Visual Studio](https://visualstudio.microsoft.com) nebo spuštění aplikace s `dotnet run` lze proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* . V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje. Další informace naleznete v tématu <xref:fundamentals/environments>.

[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .

Příklad konfigurace `HostBuilder` pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> se dá volat víckrát s přičítáním výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. Konfigurace vytvořená <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).

Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu. Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující položkou `<Content>`:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> se dá volat víckrát s přičítáním výsledků.

Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá metodu rozšíření `AddHostedService` k přidání služby pro události života, `LifetimeEventsHostedService` a časované úlohy na pozadí, `TimedHostedService`, do aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci poskytnuté <xref:Microsoft.Extensions.Logging.ILoggingBuilder>. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> se dá volat víckrát s přičítáním výsledkům.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> naslouchá CTRL + C/SIGINT nebo SIGTERM a zavolá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření, jako je například [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync). <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je předem registrován jako výchozí implementace životního cyklu. Je použita poslední registrovaná doba života.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfigurace kontejneru

Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI. [UseServiceProviderFactory (IServiceProviderFactory @ no__t-1TContainerBuilder @ no__t-2)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepíše výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.

Vlastní konfigurace kontejneru je spravovaná metodou <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> se dá volat víckrát s přičítáním výsledků.

Vytvoření kontejneru služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Poskytněte objekt pro vytváření kontejnerů služby:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>. Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder> s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) , který je znázorněn v <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikace vytváří metodu rozšíření `UseHostedService` k registraci hostované služby předané v `T`:

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

Implementace <xref:Microsoft.Extensions.Hosting.IHost> zodpovídá za spouštění a zastavování implementací <xref:Microsoft.Extensions.Hosting.IHostedService>, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spusťte

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:

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

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.

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

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> povolí podporu konzoly, sestaví a spustí hostitele a počká, až se vypne CTRL + C/SIGINT nebo SIGTERM.

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

### <a name="start-and-stopasync"></a>Spustit a StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spustí synchronního hostitele.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.

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

@no__t – 0 spustí aplikaci.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> zastaví aplikaci.

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

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spustí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako je například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (naslouchá pro CTRL + C/SIGINT nebo SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

### <a name="external-control"></a>Externí ovládací prvek

Externí kontrolu hostitele lze dosáhnout pomocí metod, které mohou být volány externě:

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

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

## <a name="ihostingenvironment-interface"></a>Rozhraní IHostingEnvironment

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace. Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, aby bylo možné použít jeho vlastnosti a metody rozšíření:

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

Další informace naleznete v tématu <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Rozhraní IApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci metod @no__t 0, které definují události spuštění a vypnutí.

| Token zrušení | Aktivováno, když&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Hostitel byl plně spuštěn. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Hostitel dokončuje řádné vypnutí. Všechny požadavky by se měly zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Hostitel provádí bezproblémové vypnutí. Žádosti se pořád dají zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |

Konstruktor – vloží službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy. [Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do třídy `LifetimeEventsHostedService` (implementace <xref:Microsoft.Extensions.Hosting.IHostedService>) k registraci událostí.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> žádosti o ukončení aplikace Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:

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
