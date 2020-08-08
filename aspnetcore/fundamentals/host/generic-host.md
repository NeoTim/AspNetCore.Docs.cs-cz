---
title: Obecný hostitel .NET
author: rick-anderson
description: Seznamte se s obecným hostitelem .NET Core, který je zodpovědný za spouštění a správu životního cyklu aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 7f662aac3714e49e6a3a63175415a1a63b0940c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017425"
---
# <a name="net-generic-host"></a>Obecný hostitel .NET

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

Šablony ASP.NET Core vytvoří obecného hostitele .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .

## <a name="host-definition"></a>Definice hostitele

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:

* Vkládání závislostí (DI)
* protokolování
* Konfigurace
* `IHostedService`implementaci

Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby. V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě. `Main`Metoda:

* Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.
* Volání `Build` a `Run` metody objektu Builder.

Webové šablony ASP.NET Core generují následující kód pro vytvoření obecného hostitele:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Následující kód vytvoří obecného hostitele pomocí úlohy jiného typu než HTTP. `IHostedService`Implementace je přidána do kontejneru di:

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

Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Předchozí kód je vygenerován šablonami ASP.NET Core.

Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace. Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:

* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s předponou `DOTNET_` .
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appsettings.js*.
  * *appSettings. {Environment}. JSON*.
  * [Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :
  * Konzola
  * Ladění
  * EventSource
  * Protokol událostí (pouze při spuštění v systému Windows)
* Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.

`ConfigureWebHostDefaults`Metoda:

* Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .
* Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .
* Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .
* Povolí integraci služby IIS. Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Služby poskytované rozhraním

Následující služby jsou registrovány automaticky:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace. Rozhraní obsahuje také `StopApplication` metodu.

Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví. Použije se Poslední registrovaná implementace.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementací. `ConsoleLifetime`:

* Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.
* Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.

Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> . Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.

Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` . `ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` . U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` . Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikací

Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` . `ConfigureAppConfiguration`dá se volat víckrát s výsledky doplňkových výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. 

Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.

**Klíč**:`applicationName`  
**Zadejte**:`string`  
**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.  
**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`

K nastavení této hodnoty použijte proměnnou prostředí. 

### <a name="contentroot"></a>ContentRoot

Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitele se nepodaří spustit.

**Klíč**:`contentRoot`  
**Zadejte**:`string`  
**Výchozí**: složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace:

* [Základy: kořenový adresář obsahu](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` . V hodnotách se nerozlišují malá a velká písmena.

**Klíč**:`environment`  
**Zadejte**:`string`  
**Výchozí**:`Production`  
**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> . Výchozí hodnota je pět sekund.  Po uplynutí časového limitu hostitel:

* Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná. Služby se zastavují i v případě, že se nedokončily zpracování. Pokud služby vyžadují ukončení déle, zvyšte časový limit.

**Klíč**:`shutdownTimeoutSeconds`  
**Zadejte**:`int`  
**Výchozí hodnota**: 5 sekund  
**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` . Následující příklad nastaví časový limit na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Nastavení pro Web Apps

Některá nastavení hostitele se vztahují jenom na úlohy HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.

`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení. Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:

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

`false`V důsledku dojde k chybám při spuštění hostitele. Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.

**Klíč**:`captureStartupErrors`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .  
**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.

**Klíč**:`detailedErrors`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`false`  
**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění. I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace. Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.

**Klíč**:`hostingStartupAssemblies`  
**Zadejte**:`string`  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.

**Klíč**:`hostingStartupExcludeAssemblies`  
**Zadejte**:`string`  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování https](xref:security/enforcing-ssl).

**Klíč**:`https_port`  
**Zadejte**:`string`  
**Výchozí**hodnota: výchozí hodnota není nastavená.  
**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.

**Klíč**:`preferHostingUrls`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`true`  
**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**:`preventHostingStartup`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`false`  
**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení, ve kterém se má hledat `Startup` Třída

**Klíč**:`startupAssembly`  
**Zadejte**:`string`  
**Výchozí**: sestavení aplikace  
**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` . `UseStartup`může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ). Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky. Například, `http://localhost:123`. Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ). Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**:`urls`  
**Zadejte**:`string`  
**Výchozí**: `http://localhost:5000` a`https://localhost:5001`  
**Proměnná prostředí**:`<PREFIX_>URLS`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace. Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.  

**Klíč**:`webroot`  
**Zadejte**:`string`  
**Výchozí**: výchozí hodnota je `wwwroot` . Cesta k *obsahu {root}/wwwroot* musí existovat.  
**Proměnná prostředí**:`<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :

```csharp
webBuilder.UseWebRoot("public");
```

Další informace:

* [Základy: web root](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Správa životnosti hostitele

Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci. Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .

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

Tento článek se zabývá ASP.NET Core obecný hostitel ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), který se používá pro aplikace, které nezpracovávají požadavky HTTP.

Účelem obecného hostitele je oddělit kanál HTTP od rozhraní API webového hostitele, aby bylo možné rozšířit pole hostitelských scénářů. Zasílání zpráv, úloh na pozadí a dalších úloh nevyužívajících HTTP na základě výhody obecného hostitele z různých možností, jako je konfigurace, vkládání závislostí (DI) a protokolování.

Obecný hostitel je v ASP.NET Core 2,1 novinkou a není vhodný pro scénáře hostování webů. U scénářů pro hostování webů použijte [webového hostitele](xref:fundamentals/host/web-host). Obecný hostitel nahradí webového hostitele v budoucí verzi a bude fungovat jako primární rozhraní API v rámci scénářů HTTP i non-HTTP.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Při spuštění ukázkové aplikace v [Visual Studio Code](https://code.visualstudio.com/)použijte *externí nebo integrovaný terminál*. Nespouštějte ukázku v `internalConsole` .

Nastavení konzoly v Visual Studio Code:

1. Otevřete soubor *. VSCode/launch.jsv* souboru.
1. V konfiguraci **spuštění .NET Core (konzola)** vyhledejte položku **Konzola** . Nastavte hodnotu buď `externalTerminal` nebo `integratedTerminal` .

## <a name="introduction"></a>Úvod

Knihovna obecných hostitelů je k dispozici v <xref:Microsoft.Extensions.Hosting> oboru názvů a poskytuje balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) . `Microsoft.Extensions.Hosting`Balíček je součástí [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).

<xref:Microsoft.Extensions.Hosting.IHostedService>je vstupním bodem pro provádění kódu. Každá <xref:Microsoft.Extensions.Hosting.IHostedService> implementace se spustí v pořadí [Registrace služby v ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>je volána při <xref:Microsoft.Extensions.Hosting.IHostedService> spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí zpětné registrace při bezproblémovém ukončení hostitele.

## <a name="set-up-a-host"></a>Nastavení hostitele

<xref:Microsoft.Extensions.Hosting.IHostBuilder>je hlavní součástí, kterou knihovny a aplikace používají k inicializaci, sestavování a spouštění hostitele:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Možnosti

<xref:Microsoft.Extensions.Hosting.HostOptions>nakonfigurujte možnosti pro <xref:Microsoft.Extensions.Hosting.IHost> .

### <a name="shutdown-timeout"></a>Časový limit vypnutí

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> . Výchozí hodnota je pět sekund.

Následující konfigurace možnosti v nástroji `Program.Main` zvyšuje výchozí časový limit pro vypnutí v pěti sekundách na 20 sekund:

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

* [Prostředí](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfigurace](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Možnosti](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )
* [Protokolování](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )

## <a name="host-configuration"></a>Konfigurace hostitele

Konfiguraci hostitele vytvořil:

* Voláním metod rozšíření pro <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavte [kořen obsahu](#content-root) a [prostředí](#environment).
* Čtení konfigurace od zprostředkovatelů konfigurace v nástroji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .

### <a name="extension-methods"></a>Metody rozšíření

### <a name="application-key-name"></a>Klíč aplikace (název)

Vlastnost [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele. Chcete-li nastavit hodnotu explicitně, použijte [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klíč**:`applicationName`  
**Zadejte**:`string`  
**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.  
**Nastavit pomocí**:`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

### <a name="content-root"></a>Kořen obsahu

Toto nastavení určuje, kde hostitel začne vyhledávat soubory obsahu.

**Klíč**:`contentRoot`  
**Zadejte**:`string`  
**Výchozí**: výchozí nastavení složky, kde se nachází sestavení aplikace.  
**Nastavit pomocí**:`UseContentRoot`  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Pokud cesta neexistuje, hostitele se nepodaří spustit.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Další informace najdete v tématu [základy: kořen obsahu](xref:fundamentals/index#content-root).

### <a name="environment"></a>Prostředí

Nastaví [prostředí](xref:fundamentals/environments)aplikace.

**Klíč**:`environment`  
**Zadejte**:`string`  
**Výchozí**:`Production`  
**Nastavit pomocí**:`UseEnvironment`  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` je [volitelná a uživatelsky definovaná](#configurehostconfiguration))

Prostředí lze nastavit na libovolnou hodnotu. Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` . V hodnotách se nerozlišují malá a velká písmena.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele. Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Ve výchozím nastavení nejsou zahrnuti žádní zprostředkovatelé. Musíte explicitně určit, v jakém poskytovateli konfigurace aplikace vyžaduje <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> , včetně:

* Konfigurace souboru (například z *hostsettings.js* souboru).
* Konfigurace proměnné prostředí.
* Konfigurace argumentů příkazového řádku.
* Všichni další povinní poskytovatelé konfigurace.

Konfigurace souborů hostitele je povolená zadáním základní cesty aplikace `SetBasePath` a následným voláním jednoho z [poskytovatelů konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider). Ukázková aplikace používá soubor JSON, *hostsettings.jszapnutý*, a volá <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> k využívání nastavení konfigurace hostitele souboru.

Chcete-li přidat [konfiguraci proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, zavolejte <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele. `AddEnvironmentVariables`přijme volitelnou uživatelem definovanou předponu. Ukázková aplikace používá předponu `PREFIX_` . Předpona je odebrána při čtení proměnných prostředí. Když je hostitel ukázkové aplikace nakonfigurovaný, hodnota proměnné prostředí `PREFIX_ENVIRONMENT` se bude hodnotou konfigurace hostitele pro tento `environment` klíč.

Během vývoje při používání sady [Visual Studio](https://visualstudio.microsoft.com) nebo při spuštění aplikace s se `dotnet run` mohou proměnné prostředí nastavit v souboru *Properties/launchSettings.js* . V [Visual Studio Code](https://code.visualstudio.com/)mohou být proměnné prostředí nastaveny v souboru *. VSCode/launch.js* při vývoji. Další informace naleznete v tématu <xref:fundamentals/environments>.

[Konfigurace příkazového řádku](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> . K povolení argumentů příkazového řádku pro přepsání konfigurace poskytované předchozími poskytovateli konfigurace se přidá poslední konfigurace příkazového řádku.

*hostsettings.js*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Další konfiguraci lze poskytnout pomocí klíčů [ApplicationName](#application-key-name) a [contentRoot](#content-root) .

Příklad `HostBuilder` Konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>dá se volat víckrát s výsledky doplňkových výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. Konfigurace vytvořená nástrojem <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*> .

Konfigurace aplikace automaticky přijímá konfiguraci hostitele, kterou poskytuje [ConfigureHostConfiguration](#configurehostconfiguration).

Příklad konfigurace aplikace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.js*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.js*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.js*:

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Chcete-li přesunout soubory nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu. Ukázková aplikace přesune své soubory nastavení aplikace JSON a *hostsettings.js* s touto `<Content>` položkou:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozšíření konfigurace, například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, například [Microsoft.Extensions.Configuration.Jsv](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [Microsoft.Extensions.Configuration. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), tyto balíčky musí být kromě balíčku Core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) přidány do projektu. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>Přidá služby do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dá se volat víckrát s výsledky doplňkových výsledků.

Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události života, `LifetimeEventsHostedService` a časově omezená úloha na pozadí, `TimedHostedService` do aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>Přidá delegáta pro konfiguraci poskytnutého <xref:Microsoft.Extensions.Logging.ILoggingBuilder> . <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>může být voláno vícekrát s přísadou výsledků.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync). `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je předem registrován jako výchozí implementace životního cyklu. Je použita poslední registrovaná doba života.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfigurace kontejneru

Pro podporu zapojení do jiných kontejnerů může hostitel přijmout <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> . Poskytnutí továrny není součástí registrace DI Container, ale je místo toho hostitel, který se používá k vytvoření konkrétního kontejneru DI. [UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí továrnu, která byla použita k vytvoření poskytovatele služeb aplikace.

Vlastní konfigurace kontejneru je spravovaná <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodou. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>poskytuje silné typové prostředí pro konfiguraci kontejneru nad podkladovým rozhraním API hostitele. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>dá se volat víckrát s výsledky doplňkových výsledků.

Vytvoření kontejneru služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Poskytněte objekt pro vytváření kontejnerů služby:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Použijte objekt pro vytváření a nakonfigurujte vlastní kontejner služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost hostitelů se provádí s metodami rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder> . Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci s příkladem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) znázorněným v <xref:fundamentals/host/hosted-services> .

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikace vytváří `UseHostedService` metodu rozšíření pro registraci hostované služby, která byla předána `T` :

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

<xref:Microsoft.Extensions.Hosting.IHost>Implementace zodpovídá za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementací, které jsou zaregistrované v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud není hostitel vypnutý:

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí:

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

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.

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

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>ukončí aplikaci.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>je aktivována prostřednictvím <xref:Microsoft.Extensions.Hosting.IHostLifetime> , například `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (naslouchá pro <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .

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

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , což čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

## <a name="ihostingenvironment-interface"></a>Rozhraní IHostingEnvironment

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>poskytuje informace o hostitelském prostředí aplikace. Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, aby <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> bylo možné použít jeho vlastnosti a metody rozšíření:

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

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umožňuje aktivity po spuštění a vypnutí, včetně řádných požadavků na vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci <xref:System.Action> metod, které definují události spuštění a vypnutí.

| Token zrušení | Aktivované při&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Hostitel byl plně spuštěn. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Hostitel dokončuje řádné vypnutí. Všechny požadavky by se měly zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Hostitel provádí bezproblémové vypnutí. Žádosti se pořád dají zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |

Konstruktor – vloží <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> službu do libovolné třídy. [Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá vkládání konstruktoru do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) pro registraci událostí.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>požaduje ukončení aplikace. Následující třída používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> k bezproblémovému vypnutí aplikace při `Shutdown` volání metody třídy:

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

::: moniker range=">= aspnetcore-5.0"

Šablony ASP.NET Core vytvoří obecného hostitele .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).

## <a name="host-definition"></a>Definice hostitele

*Hostitel* je objekt, který zapouzdřuje prostředky aplikace, jako například:

* Vkládání závislostí (DI)
* protokolování
* Konfigurace
* `IHostedService`implementaci

Při spuštění hostitele zavolá <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> každou implementaci <xref:Microsoft.Extensions.Hosting.IHostedService> zaregistrovanou v kolekci hostovaných služeb kontejneru služby. V rámci webové aplikace je jednou z `IHostedService` implementací webová služba, která spouští [implementaci HTTP serveru](xref:fundamentals/index#servers).

Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je obvykle nakonfigurovaný, sestavený a spouštěný pomocí kódu ve `Program` třídě. `Main`Metoda:

* Volá `CreateHostBuilder` metodu pro vytvoření a konfiguraci objektu Tvůrce.
* Volání `Build` a `Run` metody objektu Builder.

Webové šablony ASP.NET Core generují následující kód pro vytvoření hostitele:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Následující kód vytvoří úlohu jiného typu než HTTP s `IHostedService` implementací přidanou do kontejneru di.

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

Pro úlohy HTTP `Main` je metoda stejná, ale `CreateHostBuilder` volání `ConfigureWebHostDefaults` :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace používá Entity Framework Core, neměňte název ani signaturu `CreateHostBuilder` metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) očekávají, že budou nalezeny `CreateHostBuilder` metody, které nakonfigurují hostitele bez spuštění aplikace. Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení tvůrce

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:

* Nastaví [kořen obsahu](xref:fundamentals/index#content-root) na cestu vrácenou <xref:System.IO.Directory.GetCurrentDirectory*> .
* Načte konfiguraci hostitele z:
  * Proměnné prostředí s předponou `DOTNET_` .
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace z:
  * *appsettings.js*.
  * *appSettings. {Environment}. JSON*.
  * [Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v `Development` prostředí
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující zprostředkovatele [protokolování](xref:fundamentals/logging/index) :
  * Konzola
  * Ladění
  * EventSource
  * Protokol událostí (pouze při spuštění v systému Windows)
* Povolí [ověřování oboru](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) při vývoji prostředí.

`ConfigureWebHostDefaults`Metoda:

* Načte konfiguraci hostitele z proměnných prostředí s předponou `ASPNETCORE_` .
* Nastaví server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a nakonfiguruje ho pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options> .
* Přidá [middleware pro filtrování hostitele](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je rovno `true` .
* Povolí integraci služby IIS. Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options> .

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro webové aplikace](#settings-for-web-apps) v části dále v tomto článku ukazují, jak přepsat výchozí nastavení tvůrce.

## <a name="framework-provided-services"></a>Služby poskytované rozhraním

Následující služby jsou registrovány automaticky:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Další informace o službách poskytovaných rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services> .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Vložení <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> služby (dříve `IApplicationLifetime` ) do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení použité k registraci metod spuštění aplikace a obslužné rutiny události zastavení aplikace. Rozhraní obsahuje také `StopApplication` metodu.

Následující příklad je `IHostedService` implementace, která registruje `IHostApplicationLifetime` události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementace určuje, kdy se hostitel spustí a kdy se zastaví. Použije se Poslední registrovaná implementace.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`je výchozí `IHostLifetime` implementací. `ConsoleLifetime`:

* Naslouchá <kbd>klávesám CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM a voláním <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> ke spuštění procesu vypnutí.
* Odblokuje rozšíření jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Vložením <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy získáte informace o následujících nastaveních:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentroot)

Webové aplikace implementují `IWebHostEnvironment` rozhraní, které dědí `IHostEnvironment` a přidává rozhraní [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.

Konfigurace hostitele je k dispozici z [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> . Po `ConfigureAppConfiguration` nahrazení se v `HostBuilderContext.Configuration` konfiguraci aplikace nahradí.

Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder` . `ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků. Hostitel používá bez ohledu na to, jaká možnost nastaví hodnotu jako poslední na daném klíči.

Zprostředkovatel proměnné prostředí s argumenty předpony `DOTNET_` a příkazového řádku jsou obsaženy v `CreateDefaultBuilder` . U webových aplikací se přidá zprostředkovatel proměnné prostředí s předponou `ASPNETCORE_` . Předpona je odebrána při čtení proměnných prostředí. Například hodnota proměnné prostředí pro se nastaví jako `ASPNETCORE_ENVIRONMENT` hodnota konfigurace hostitele pro `environment` klíč.

Následující příklad vytvoří konfiguraci hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikací

Konfigurace aplikace je vytvořena voláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> metody `IHostBuilder` . `ConfigureAppConfiguration`dá se volat víckrát s výsledky doplňkových výsledků. Aplikace používá možnost, která pro daný klíč nastaví hodnotu Last. 

Konfigurace vytvořená nástrojem `ConfigureAppConfiguration` je k dispozici na adrese [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a jako službu z di. Konfigurace hostitele je také přidána do konfigurace aplikace.

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

V této části jsou uvedená nastavení hostitele, která se vztahují na úlohy HTTP i bez HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

Vlastnost [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) je nastavena v konfiguraci hostitele během vytváření hostitele.

**Klíč**:`applicationName`  
**Zadejte**:`string`  
**Výchozí**: název sestavení, které obsahuje vstupní bod aplikace.  
**Proměnná prostředí**:`<PREFIX_>APPLICATIONNAME`

K nastavení této hodnoty použijte proměnnou prostředí. 

### <a name="contentroot"></a>ContentRoot

Vlastnost [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) určuje, kde hostitel začne vyhledávat soubory obsahu. Pokud cesta neexistuje, hostitele se nepodaří spustit.

**Klíč**:`contentRoot`  
**Zadejte**:`string`  
**Výchozí**: složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**:`<PREFIX_>CONTENTROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseContentRoot` na `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Další informace:

* [Základy: kořenový adresář obsahu](xref:fundamentals/index#content-root)
* [WebRoot](#webroot)

### <a name="environmentname"></a>EnvironmentName

Vlastnost [IHostEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) lze nastavit na libovolnou hodnotu. Hodnoty definované rozhraním zahrnují `Development` , `Staging` a `Production` . V hodnotách se nerozlišují malá a velká písmena.

**Klíč**:`environment`  
**Zadejte**:`string`  
**Výchozí**:`Production`  
**Proměnná prostředí**:`<PREFIX_>ENVIRONMENT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseEnvironment` na `IHostBuilder` :

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions. ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> . Výchozí hodnota je pět sekund.  Po uplynutí časového limitu hostitel:

* Spustí [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb a protokolování chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná. Služby se zastavují i v případě, že se nedokončily zpracování. Pokud služby vyžadují ukončení déle, zvyšte časový limit.

**Klíč**:`shutdownTimeoutSeconds`  
**Zadejte**:`int`  
**Výchozí hodnota**: 5 sekund  
**Proměnná prostředí**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

K nastavení této hodnoty použijte proměnnou prostředí nebo nakonfigurujte `HostOptions` . Následující příklad nastaví časový limit na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Zakázat opětovné načtení konfigurace aplikace při změně

Ve [výchozím nastavení](xref:fundamentals/configuration/index#default) *appsettings.jsv* a *appSettings. { Prostředí}. JSON* se při změně souboru znovu načte. Pokud chcete toto chování při opětovném načítání zakázat v ASP.NET Core 5,0 Preview 3 nebo novějším, nastavte `hostBuilder:reloadConfigOnChange` klíč na `false` .

**Klíč**:`hostBuilder:reloadConfigOnChange`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`true`  
**Argument příkazového řádku**:`hostBuilder:reloadConfigOnChange`  
**Proměnná prostředí**:`<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> Oddělovač dvojtečky ( `:` ) nefunguje s hierarchickými klíči proměnné prostředí na všech platformách. Další informace naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).

## <a name="settings-for-web-apps"></a>Nastavení pro Web Apps

Některá nastavení hostitele se vztahují jenom na úlohy HTTP. Ve výchozím nastavení proměnné prostředí použité pro konfiguraci těchto nastavení mohou mít `DOTNET_` `ASPNETCORE_` předponu nebo.

`IWebHostBuilder`K dispozici jsou metody rozšíření pro tato nastavení. Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` , je instancí `IWebHostBuilder` , jako v následujícím příkladu:

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

`false`V důsledku dojde k chybám při spuštění hostitele. Když `true` hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.

**Klíč**:`captureStartupErrors`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**: výchozí nastavení `false` , pokud se aplikace nespustí s Kestrel za IIS, kde je výchozí hodnota `true` .  
**Proměnná prostředí**:`<PREFIX_>CAPTURESTARTUPERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `CaptureStartupErrors` :

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Pokud je povoleno nebo když je prostředí `Development` , aplikace zachycuje podrobné chyby.

**Klíč**:`detailedErrors`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`false`  
**Proměnná prostředí**:`<PREFIX_>_DETAILEDERRORS`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění. I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace. Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.

**Klíč**:`hostingStartupAssemblies`  
**Zadejte**:`string`  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.

**Klíč**:`hostingStartupExcludeAssemblies`  
**Zadejte**:`string`  
**Výchozí**: prázdný řetězec  
**Proměnná prostředí**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port přesměrování HTTPS. Používá se při [vynucování https](xref:security/enforcing-ssl).

**Klíč**:`https_port`  
**Zadejte**:`string`  
**Výchozí**hodnota: výchozí hodnota není nastavená.  
**Proměnná prostředí**:`<PREFIX_>HTTPS_PORT`

Chcete-li nastavit tuto hodnotu, použijte konfiguraci nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným pomocí a `IWebHostBuilder` místo adres URL nakonfigurovaných `IServer` implementací.

**Klíč**:`preferHostingUrls`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`true`  
**Proměnná prostředí**:`<PREFIX_>_PREFERHOSTINGURLS`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `PreferHostingUrls` :

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**:`preventHostingStartup`  
**Typ**: `bool` ( `true` nebo `1` )  
**Výchozí**:`false`  
**Proměnná prostředí**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení, ve kterém se má hledat `Startup` Třída

**Klíč**:`startupAssembly`  
**Zadejte**:`string`  
**Výchozí**: sestavení aplikace  
**Proměnná prostředí**:`<PREFIX_>STARTUPASSEMBLY`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup` . `UseStartup`může převzít název sestavení ( `string` ) nebo typ ( `TStartup` ). Pokud `UseStartup` je voláno více metod, má poslední z nich přednost.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Středníkem oddělený seznam IP adres nebo adres hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky. Například, `http://localhost:123`. Pomocí příkazu " \* " určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu (například `http://*:5000` ). Protokol ( `http://` nebo `https://` ) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

**Klíč**:`urls`  
**Zadejte**:`string`  
**Výchozí**: `http://localhost:5000` a`https://localhost:5001`  
**Proměnná prostředí**:`<PREFIX_>URLS`

K nastavení této hodnoty použijte proměnnou prostředí nebo volání `UseUrls` :

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Vlastnost [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) Určuje relativní cestu ke statickým assetům aplikace. Pokud cesta neexistuje, použije se zprostředkovatel souborů no-op.  

**Klíč**:`webroot`  
**Zadejte**:`string`  
**Výchozí**: výchozí hodnota je `wwwroot` . Cesta k *obsahu {root}/wwwroot* musí existovat.  
**Proměnná prostředí**:`<PREFIX_>WEBROOT`

Chcete-li nastavit tuto hodnotu, použijte proměnnou prostředí nebo zavolejte `UseWebRoot` na `IWebHostBuilder` :

```csharp
webBuilder.UseWebRoot("public");
```

Další informace:

* [Základy: web root](xref:fundamentals/index#web-root)
* [ContentRoot](#contentroot)

## <a name="manage-the-host-lifetime"></a>Správa životnosti hostitele

Voláním metod v sestavené <xref:Microsoft.Extensions.Hosting.IHost> implementaci spustíte a zastavíte aplikaci. Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou zaregistrovány v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>spustí aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>spustí aplikaci a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při aktivaci tokenu zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>Povolí podporu konzoly, sestaví a spustí hostitele a počká, <kbd>Ctrl</kbd> + až se vypne CTRL<kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>spustí synchronního hostitele.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje token zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>je volána na začátku `StartAsync` , což čeká na dokončení před pokračováním. Dá se použít ke zpoždění spuštění, dokud se nesignalizuje externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>pokusy o zastavení hostitele v zadaném časovém limitu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje volající vlákno, dokud se neaktivuje IHostLifetime, například pomocí <kbd>CTRL</kbd> + <kbd>C</kbd>/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Vrátí a <xref:System.Threading.Tasks.Task> , který se dokončí, když se aktivuje při vypnutí prostřednictvím daného tokenu a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .

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

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/host/hosted-services>
