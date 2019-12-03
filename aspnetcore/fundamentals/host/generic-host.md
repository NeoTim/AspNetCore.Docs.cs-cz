---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 2ed4af109b5ccd303a03a0d9167649dda7793126
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717019"
---
# <a name="net-generic-host"></a>Obecný hostitel .NET

::: moniker range=">= aspnetcore-3.0"

Tento článek představuje obecného hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny k jeho použití.

## <a name="whats-a-host"></a>Co je hostitel?

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:

* Vkládání závislostí (DI)
* protokolování
* Konfigurace
* `IHostedService` implementace

Při spuštění hostitele volá `IHostedService.StartAsync` na každé implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>, kterou nalezne v kontejneru DI. V rámci webové aplikace je jedním z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.

Ve verzích ASP.NET Core starších než 3,0 se [webový hostitel](xref:fundamentals/host/web-host) používá pro úlohy http. Webový hostitel již není doporučen pro webové aplikace a zůstává dostupný pouze pro zpětnou kompatibilitu.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve třídě `Program`. Metoda `Main`:

* Volá metodu `CreateHostBuilder` pro vytvoření a konfiguraci objektu Tvůrce.
* Volá `Build` a `Run` metody objektu Builder.

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

Pro úlohy HTTP je metoda `Main` stejná, ale `CreateHostBuilder` volá `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že se najde `CreateHostBuilder` metoda, která nakonfiguruje hostitele bez spuštění aplikace. Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce

Metoda <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:

* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*>.
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s předponou "DOTNET_".
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appSettings. JSON*.
  * *appSettings. {Environment}. JSON*.
  * [Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development`.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :
  * Konzola
  * Ladit
  * EventSource
  * Protokol událostí (pouze při spuštění v systému Windows)
* Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.

Metoda `ConfigureWebHostDefaults`:

* Načte konfiguraci hostitele z proměnných prostředí s předponou "ASPNETCORE_".
* Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.
* Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.
* Povolí integraci služby IIS. Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Služby poskytované rozhraním

Mezi služby, které jsou zaregistrovány automaticky, patří následující:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Vložení služby <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dřív `IApplicationLifetime`) do libovolné třídy pro zpracování úloh po spuštění a řádném vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace. Rozhraní obsahuje také metodu `StopApplication`.

Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Implementace <xref:Microsoft.Extensions.Hosting.IHostLifetime> určuje, kdy se hostitel spustí a kdy se zastaví. Použije se Poslední registrovaná implementace.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je výchozí `IHostLifetime` implementace. `ConsoleLifetime`:

* Naslouchá pro kombinaci kláves CTRL + C/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> pro spuštění procesu vypnutí.
* Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Vložením služby <xref:Microsoft.Extensions.Hosting.IHostEnvironment> do třídy získáte informace o následujícím:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentrootpath)

Webové aplikace implementují rozhraní `IWebHostEnvironment`, které dědí `IHostEnvironment` a přidává [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro vlastnosti implementace <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.

Konfigurace hostitele je k dispozici z [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) v rámci <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. Po `ConfigureAppConfiguration`se `HostBuilderContext.Configuration` nahradí konfigurací aplikace.

Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> v `IHostBuilder`. `ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou součástí CreateDefaultBuilder. U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_`. Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikace

Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`. `ConfigureAppConfiguration` lze volat vícekrát pomocí aditivních výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. 

Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.

**Klíč**: ApplicationName  
**Typ**: *řetězec*  
**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`

K nastavení této hodnoty použijte proměnnou prostředí. 

### <a name="contentrootpath"></a>ContentRootPath

Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitele se nepodaří spustit.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseContentRoot` volání v `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace najdete v části .

* [Základy: kořenový adresář obsahu](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`. U hodnot se nerozlišují malá a velká písmena.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: výroba  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseEnvironment` volání v `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.  Po uplynutí časového limitu hostitel:

* Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná. Služby se zastavují i v případě, že se nedokončily zpracování. Pokud služby vyžadují ukončení déle, zvyšte časový limit.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: **Proměnná prostředí**o 5 sekund: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions`. Následující příklad nastaví časový limit na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Nastavení pro Web Apps

Některá nastavení hostitele se vztahují jenom na úlohy HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít předponu `DOTNET_` nebo `ASPNETCORE_`.

Pro tato nastavení jsou k dispozici metody rozšíření na `IWebHostBuilder`. Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:

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

Při `false`dojde k chybám při spuštění hostitele. Při `true`zachytí hostitel při spuštění výjimky a pokusí se spustit server.

**Klíč**: captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: výchozí nastavení `false`, pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true`.  
**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte `CaptureStartupErrors`konfigurace nebo volání:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Pokud je povoleno nebo když je prostředí `Development`, aplikace zachycuje podrobné chyby.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění. I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace. Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.

**Klíč**: hostingStartupExcludeAssemblies  
**Typ**: *řetězec*  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování https](xref:security/enforcing-ssl).

**Klíč**: https_port  
**Typ**: *řetězec*  
**Výchozí**hodnota: výchozí hodnota není nastavená.  
**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte `UseSetting`konfigurace nebo volání:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Určuje, zda má hostitel naslouchat na adresách URL konfigurovaných pomocí `IWebHostBuilder` namísto těch nakonfigurovaných pomocí implementace `IServer`.

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `PreferHostingUrls`volání:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**: preventHostingStartup  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseSetting` volání:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení, ve kterém se má hledat `Startup` třídy

**Klíč**: startupAssembly  
**Typ**: *řetězec*  
**Výchozí**: sestavení aplikace  
**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`. `UseStartup` může převzít název sestavení (`string`) nebo typ (`TStartup`). Je-li voláno více metod `UseStartup`, má poslední název přednost.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky. Například `http://localhost:123`. Pomocí příkazu "\*" označíte, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000`). Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**: adresy URL  
**Typ**: *řetězec*  
**Výchozí**: `http://localhost:5000` a `https://localhost:5001`  
**Proměnná prostředí**: `<PREFIX_>URLS`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseUrls`volání:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu. Další informace najdete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Relativní cesta ke statickým prostředkům aplikace

**Klíč**: Webroot  
**Typ**: *řetězec*  
**Výchozí**: výchozí hodnota je `wwwroot`. Cesta k *obsahu {root}/wwwroot* musí existovat. Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.  
**Proměnná prostředí**: `<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo `UseWebRoot`volání:

```csharp
webBuilder.UseWebRoot("public");
```

Další informace najdete v části .

* [Základy: web root](xref:fundamentals/index#web-root)
* [ContentRootPath](#contentrootpath)

## <a name="manage-the-host-lifetime"></a>Správa životnosti hostitele

Volejte metody pro sestavenou <xref:Microsoft.Extensions.Hosting.IHost> implementaci pro spuštění a zastavení aplikace. Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí při aktivaci tokenu zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí CTRL + C/SIGINT nebo SIGTERM.

### <a name="start"></a>Začátek

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí hostitele a vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje token zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> se volá na začátku `StartAsync`, která čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> se pokusí zastavit hostitele v zadaném časovém limitu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například prostřednictvím CTRL + C/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

ASP.NET Core aplikace nakonfigurují a spouštějí hostitele. Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.

Tento článek se zabývá ASP.NET Core obecný hostitel (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které nezpracovávají požadavky HTTP.

Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů. Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.

Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů. U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host). Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*. Nespouštějte ukázku ve `internalConsole`.

Nastavení konzoly v Visual Studio Code:

1. Otevřete soubor *. VSCode/Launch. JSON* .
1. V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** . Nastavte hodnotu buď `externalTerminal`, nebo `integratedTerminal`.

## <a name="introduction"></a>Úvod

Knihovna obecných hostitelů je k dispozici v oboru názvů <xref:Microsoft.Extensions.Hosting> a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) . Balíček `Microsoft.Extensions.Hosting` obsahuje [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).

<xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem pro provádění kódu. Jednotlivé implementace <xref:Microsoft.Extensions.Hosting.IHostedService> jsou spouštěny v pořadí [Registrace služby v ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> se volá na každém <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> se volá v pořadí zpětné registrace při bezproblémovém vypnutí hostitele.

## <a name="set-up-a-host"></a>Nastavení hostitele

<xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní součást, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Možnosti

<xref:Microsoft.Extensions.Hosting.HostOptions> nakonfigurovat možnosti pro <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Časový limit vypnutí

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.

Následující konfigurace možnosti v `Program.Main` zvyšuje výchozí časový limit pěti sekund vypnutí na 20 sekund:

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
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Možnosti](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Protokolování](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konfigurace hostitele

Konfiguraci hostitele vytvořil:

* Volání rozšiřujících metod v <xref:Microsoft.Extensions.Hosting.IHostBuilder> pro nastavení [kořenového adresáře obsahu](#content-root) a [prostředí](#environment).
* Čtení konfigurace od zprostředkovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.

### <a name="extension-methods"></a>Metody rozšíření

### <a name="application-key-name"></a>Klíč aplikace (název)

Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele. Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klíč**: ApplicationName  
**Typ**: *řetězec*  
**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.  
**Nastavit pomocí**: `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

### <a name="content-root"></a>Kořen obsahu

Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.  
**Nastavit pomocí**: `UseContentRoot`  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Pokud cesta neexistuje, hostitele se nepodaří spustit.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).

### <a name="environment"></a>Prostředí

Nastaví [prostředí](xref:fundamentals/environments)aplikace.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: výroba  
**Nastavit pomocí**: `UseEnvironment`  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Prostředí lze nastavit na libovolnou hodnotu. Mezi hodnoty definované rozhraní patří `Development`, `Staging`a `Production`. U hodnot se nerozlišují malá a velká písmena.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele. Konfigurace hostitele se používá k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> lze volat vícekrát pomocí aditivních výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé. Musíte explicitně zadat, které poskytovatelé konfigurace aplikace vyžaduje v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:

* Konfigurace souboru (například ze souboru *HostSettings. JSON* ).
* Konfigurace proměnné prostředí.
* Konfigurace argumentů příkazového řádku.
* Všichni další povinní poskytovatelé konfigurace.

Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace s `SetBasePath` následovaným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider). Ukázková aplikace používá soubor JSON *HostSettings. JSON*a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> ke využívání nastavení konfigurace hostitele souboru.

Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v Tvůrci hostitele. `AddEnvironmentVariables` akceptuje volitelnou uživatelem definovanou předponu. Ukázková aplikace používá předponu `PREFIX_`. Předpona je odebrána při čtení proměnných prostředí. Když je nakonfigurován hostitel ukázkové aplikace, hodnota proměnné prostředí pro `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro `environment` klíč.

Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s `dotnet run`lze proměnné prostředí nastavit v souboru *Properties/launchSettings. JSON* . V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/Launch. JSON* během vývoje. Další informace najdete v tématu <xref:fundamentals/environments>.

[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.

*HostSettings. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .

Příklad `HostBuilder` konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Konfigurace aplikace se vytvoří voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> v implementaci <xref:Microsoft.Extensions.Hosting.IHostBuilder>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> lze volat vícekrát pomocí aditivních výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext. Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).

Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appSettings. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appSettings. Vývoj. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appSettings. Produkční. JSON*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu. Ukázková aplikace přesune své soubory nastavení aplikace JSON a *HostSettings. JSON* s následující položkou `<Content>`:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft. Extensions. Configuration. JSON](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft. Extensions. Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě základního balíčku [Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu. Další informace najdete v tématu <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> lze volat vícekrát pomocí aditivních výsledků.

Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. Další informace najdete v tématu <xref:fundamentals/host/hosted-services>.

[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá metodu rozšíření `AddHostedService` k přidání služby pro události života, `LifetimeEventsHostedService`a časově omezená úloha na pozadí, `TimedHostedService`do aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci poskytnuté <xref:Microsoft.Extensions.Logging.ILoggingBuilder>. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být voláno vícekrát s doplňkovou výsledků.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> naslouchá kombinaci kláves CTRL + C/SIGINT nebo SIGTERM a volá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync). `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` je předem registrován jako výchozí implementace životního cyklu. Je použita poslední registrovaná doba života.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfigurace kontejneru

Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI. [UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.

Vlastní konfigurace kontejneru je spravovaná metodou <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> lze volat vícekrát pomocí aditivních výsledků.

Vytvoření kontejneru služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Poskytněte objekt pro vytváření kontejnerů služby:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>. Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikace vytvoří metodu rozšíření `UseHostedService` k registraci hostované služby předané v `T`:

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

Implementace <xref:Microsoft.Extensions.Hosting.IHost> zodpovídá za spuštění a zastavení <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrované v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task>, která se dokončí, když se aktivuje token zrušení nebo vypnutí.

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

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestavování a spouštění hostitele a čeká na vypnutí CTRL + C/SIGINT nebo SIGTERM.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> spouští hostitele synchronně.

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

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spustí aplikaci.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spustí prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako je například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro CTRL + C/SIGINT nebo SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> vrátí <xref:System.Threading.Tasks.Task>, který se dokončí, když se aktivuje prostřednictvím daného tokenu a volá <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o hostitelském prostředí aplikace. Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) k získání <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>, aby bylo možné použít jeho vlastnosti a metody rozšíření:

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

Další informace najdete v tématu <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Rozhraní IApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.

| Token zrušení | Aktivováno, když&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Hostitel byl plně spuštěn. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Hostitel dokončuje řádné vypnutí. Všechny požadavky by se měly zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Hostitel provádí bezproblémové vypnutí. Žádosti se pořád dají zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |

Konstruktor – vloží službu <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> do libovolné třídy. [Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy (implementace <xref:Microsoft.Extensions.Hosting.IHostedService>) k registraci událostí.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> žádosti o ukončení aplikace. Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace, když je volána metoda `Shutdown` třídy:

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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/host/hosted-services>
