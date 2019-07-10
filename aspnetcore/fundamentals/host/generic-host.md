---
title: .NET Generic Host
author: tdykstra
description: Informace o obecných hostitele .NET Core, který je zodpovědný za spouštění a životního cyklu správy aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: d787559eaecd6d4d6cfe01e37baf28774a90c5c3
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724424"
---
# <a name="net-generic-host"></a>.NET Generic Host

::: moniker range=">= aspnetcore-3.0"

Tento článek představuje obecné hostitele .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) a poskytuje pokyny o tom, jak ho použít.

## <a name="whats-a-host"></a>Co je hostitele?

A *hostitele* je objekt, který zapouzdřuje prostředky vaší aplikace, jako například:

* Injektáž závislostí (DI)
* Protokolování
* Konfiguraci
* `IHostedService` Implementace

Při spuštění hostitele, které volá `IHostedService.StartAsync` na každé provedení <xref:Microsoft.Extensions.Hosting.IHostedService> nalezenému v kontejneru DI. Ve webové aplikaci, jeden z `IHostedService` implementace je webová služba, která se spustí [implementaci serveru HTTP](xref:fundamentals/index#servers).

Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.

Ve verzích starších než 3.0, ASP.NET Core [webového hostitele](xref:fundamentals/host/web-host) se používá pro úlohy HTTP. Webového hostitele už se nedoporučuje pro webové aplikace a zůstane k dispozici pouze z důvodu zpětné kompatibility.

## <a name="set-up-a-host"></a>Nastavení hostitele

Hostitel je typicky nakonfigurován, sestavení a spuštění kódu v `Program` třídy. `Main` Metody:

* Volání `CreateHostBuilder` metody vytvoření a konfigurace objekt Tvůrce.
* Volání `Build` a `Run` metody Tvůrce objektu.

Tady je *Program.cs* kód pro úlohu jiným protokolem než HTTP pomocí jediného `IHostedService` DI kontejneru přidá implementace. 

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

Úloh HTTP `Main` metodou je stejný ale `CreateHostBuilder` volání `ConfigureWebHostDefaults`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pokud aplikace využívá Entity Framework Core, neměňte název nebo podpis `CreateHostBuilder` metody. [Nástroje Entity Framework Core](/ef/core/miscellaneous/cli/) chcete vyhledat `CreateHostBuilder` metodu, která nakonfiguruje hostitele bez spuštění aplikace. Další informace najdete v tématu [vytváření DbContext v době návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Výchozí nastavení Tvůrce 

<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Metody:

* Nastaví obsahu kořenovou cesta vrácená procedurou <xref:System.IO.Directory.GetCurrentDirectory*>.
* Načítání z konfigurace hostitele:
  * Proměnné prostředí s předponou "DOTNET_".
  * Argumenty příkazového řádku.
* Konfigurace aplikace načte z:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Tajný klíč správce](xref:security/app-secrets) při spuštění aplikace `Development` prostředí.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Přidá následující [protokolování](xref:fundamentals/logging/index) zprostředkovatelé:
  * Konzola
  * Ladění
  * EventSource
  * Protokol událostí (pouze při spuštění na Windows)
* Umožňuje [oboru ověření](xref:fundamentals/dependency-injection#scope-validation) a [ověřování závislostí](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) po vývojové prostředí.

`ConfigureWebHostDefaults` Metody:

* Načtení hostovat konfiguraci z proměnné prostředí s předponou "ASPNETCORE_".
* Nastaví [Kestrel](xref:fundamentals/servers/kestrel) serveru webového serveru a nakonfiguruje ho pomocí aplikace poskytovatelů konfigurace. Možnosti výchozí Kestrel serveru najdete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.
* Přidá [hostitele filtrování middleware](xref:fundamentals/servers/kestrel#host-filtering).
* Přidá [předané záhlaví middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) Pokud ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.
* Umožňuje integraci služby IIS. Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.

[Nastavení pro všechny typy aplikací](#settings-for-all-app-types) a [nastavení pro službu web apps](#settings-for-web-apps) částech dále v tomto článku ukazují, jak přepsat výchozí nastavení Tvůrce.

## <a name="framework-provided-services"></a>Služby poskytované frameworkem

Služby, které jsou registrovány automaticky zahrnují následující:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Seznam všechny služby poskytované rozhraním najdete v tématu <xref:fundamentals/dependency-injection#framework-provided-services>.

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

Vložit <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (dříve `IApplicationLifetime`) service do libovolné třídy pro zpracování úloh po spuštění a řádné vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci aplikace počáteční a obslužné rutiny události zastavení aplikace. Zahrnuje také rozhraní `StopApplication` metody.

Následující příklad je určený `IHostedService` implementace, která se registruje `IApplicationLifetime` události:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

<xref:Microsoft.Extensions.Hosting.IHostLifetime> Implementace řídí při spuštění hostitele a při zastavení. Poslední implementace registrována se používá.

<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> Výchozí hodnota je `IHostLifetime` implementace. `ConsoleLifetime`:

* čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí.
* Odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostEnvironment

Vložit <xref:Microsoft.Extensions.Hosting.IHostEnvironment> služby do třídy a získat informace o následující:

* [ApplicationName](#applicationname)
* [EnvironmentName](#environmentname)
* [ContentRootPath](#contentrootpath)

Webové aplikace implementovat `IWebHostEnvironment` rozhraní, která dědí `IHostEnvironment` a přidá:

* [WebRootPath](#webroot)

## <a name="host-configuration"></a>Konfigurace hostitele

Konfigurace hostitele se používá pro vlastnosti <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementace.

Konfigurace hostitele je k dispozici [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) uvnitř <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>. Po `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` nahradí se konfigurace aplikace.

Chcete-li přidat konfiguraci hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> na `IHostBuilder`. `ConfigureHostConfiguration` můžete volat vícekrát s přičítáním výsledky. Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.

Zprostředkovatel proměnné prostředí s předponou `DOTNET_` a argumenty příkazového řádku jsou zahrnuty CreateDefaultBuilder. Pro webové aplikace, poskytovatel proměnné prostředí s předponou `ASPNETCORE_` je přidána. Předpona, která se odebere, když jsou proměnné prostředí načteny. Například, hodnotu proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.

Následující příklad vytvoří konfigurace hostitele:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfigurace aplikace

Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na `IHostBuilder`. `ConfigureAppConfiguration` můžete volat vícekrát s přičítáním výsledky. Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč. 

Konfigurace vytvořil `ConfigureAppConfiguration` je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a as a service od DI. Konfigurace hostitele je taky přidaný ke konfiguraci aplikací.

Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Nastavení pro všechny typy aplikací

Tato část obsahuje seznam nastavení hostitele, které se vztahují na HTTP a jiným protokolem než HTTP úlohy. Ve výchozím nastavení, můžete použít ke konfiguraci těchto nastavení proměnné prostředí mají `DOTNET_` nebo `ASPNETCORE_` předponu.

### <a name="applicationname"></a>ApplicationName

[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele.

**Klíč**: applicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení, který obsahuje vstupní aplikace bodu.
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí. 

### <a name="contentrootpath"></a>ContentRootPath

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) vlastnost určuje, kde začíná hostitele vyhledávání obsahu souborů. Pokud cesta neexistuje, hostitel se nepodaří spustit.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Složka, ve které se nachází sestavení aplikace.  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseContentRoot` na `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a>EnvironmentName

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) vlastnost lze nastavit na libovolnou hodnotu. Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`. Hodnoty se velká a malá písmena.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: Produkční  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseEnvironment` na `IHostBuilder`:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>ShutdownTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.  Během období časového limitu hostitele:

* Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Limitu pokusí zastavit hostovaných služeb, protokolování chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před všechny zarážky hostovaných služeb, zastaví se všechny zbývající služby active při ukončení aplikace. Zastavení služeb i v případě, že se nedokončilo zpracování. Pokud služby vyžadují další čas ukončení, zvýšit časový limit.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: 5 sekund **proměnnou prostředí**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Tuto hodnotu nastavit pomocí proměnné prostředí nebo konfigurace `HostOptions`. Následující příklad nastaví časový limit na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Nastavení pro webové aplikace

Některá nastavení hostitele platí pouze pro úlohy HTTP. Ve výchozím nastavení, můžete použít ke konfiguraci těchto nastavení proměnné prostředí mají `DOTNET_` nebo `ASPNETCORE_` předponu.

Rozšiřující metody na `IWebHostBuilder` jsou dostupné pro tato nastavení. Ukázky kódu, které ukazují, jak volat metody rozšíření předpokládají `webBuilder` je instance `IWebHostBuilder`, jako v následujícím příkladu:

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

Když `false`, chyby během spuštění výsledku na hostiteli. operace bude ukončena. Když `true`, zachytí výjimky během spouštění hostitele a se pokusí spustit na serveru.

**Klíč**: captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: Výchozí hodnota je `false` Pokud aplikace běží s Kestrel za služby IIS, kde je jako výchozí `true`.  
**Proměnná prostředí**: `<PREFIX_>CAPTURESTARTUPERRORS`

Nastavit tuto hodnotu použít konfigurace nebo volání `CaptureStartupErrors`:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>DetailedErrors

Když povolena, nebo když je prostředí `Development`, aplikace zaznamenává podrobné chyby.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_DETAILEDERRORS`

Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Středníkem oddělený řetězec hostování načíst při spuštění po spuštění sestavení. I když výchozí hodnota konfigurace je prázdný řetězec, hostování při spuštění sestavení vždy zahrnovat sestavení aplikace. Při hostování při spuštění sestavení jsou k dispozici, se přidají do sestavení aplikace pro načtení, když aplikace je sestavena své běžné služby během spouštění.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Řetězec oddělený středníkem při spuštění sestavení mají vyloučit při spuštění hostování.

**Klíč**: hostingStartupExcludeAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Proměnná prostředí**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="httpsport"></a>HTTPS_Port

HTTPS přesměrování portu. Použít v [vynucování HTTPS](xref:security/enforcing-ssl).

**Klíč**: https_port **typ**: *řetězec*
**výchozí**: Výchozí hodnota není nastavená.
**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT`

Nastavit tuto hodnotu použít konfigurace nebo volání `UseSetting`:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls

Určuje, zda hostitel naslouchat požadavkům na adresy URL nakonfigurované `IWebHostBuilder` místo nastavení nakonfigurovaného pomocí `IServer` implementace.

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Proměnná prostředí**: `<PREFIX_>_PREFERHOSTINGURLS`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `PreferHostingUrls`:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Brání automatické načítání hostování při spuštění sestavení, včetně hostování při spuštění sestavení nakonfiguroval sestavení aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**: preventHostingStartup  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Proměnná prostředí**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseSetting` :

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Sestavení pro hledání `Startup` třídy.

**Klíč**: startupAssembly **typ**: *řetězec*  
**Výchozí**: Sestavení aplikace  
**Proměnná prostředí**: `<PREFIX_>STARTUPASSEMBLY`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseStartup`. `UseStartup` může trvat název sestavení (`string`) nebo typu (`TStartup`). Pokud je položek víc `UseStartup` metody jsou volány, má přednost před poslední z nich.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL – adresy

Středníkem oddělený seznam IP adres nebo adres hostitele s porty a protokoly, které server naslouchat požadavkům na požadavky. Například, `http://localhost:123`. Použití "\*" k označení, že by měl server naslouchat požadavkům na IP adresu nebo název hostitele zadaný port a protokol (například `http://*:5000`). Protokol (`http://` nebo `https://`) musí být součástí jednotlivé adresy URL. Podporované formáty lišit mezi servery.

**Klíč**: adresy URL  
**Typ**: *řetězec*  
**Výchozí**: `http://localhost:5000` a `https://localhost:5001` 
 **proměnnou prostředí**: `<PREFIX_>URLS`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseUrls`:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kestrel má svůj vlastní konfigurace koncového bodu rozhraní API. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>WebRoot

Relativní cesta k statických prostředků aplikace.

**Klíč**: webroot  
**Typ**: *řetězec*  
**Výchozí**: *(Obsah kořenové) / wwwroot*, pokud cesta existuje. Pokud cesta neexistuje, no-op soubor zprostředkovatele se používá.  
**Proměnná prostředí**: `<PREFIX_>WEBROOT`

Pokud chcete nastavit tuto hodnotu, použijte proměnnou prostředí nebo volání `UseWebRoot`:

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a>Správa životního cyklu hostitelů

Volání metod na vestavěný <xref:Microsoft.Extensions.Hosting.IHost> implementace spuštění a zastavení aplikace. Tyto metody ovlivňují všechny <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM vypnout.

### <a name="start"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> Spustí hostitele a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku `StartAsync`, která čeká, dokud neskončí než budete pokračovat. To umožňuje zpoždění spuštění, dokud signalizován externí událostí.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blokuje volající vlákno, dokud se vypnutí se aktivuje IHostLifetime, například prostřednictvím kombinace kláves Ctrl + C/SIGINT nebo SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

### <a name="external-control"></a>Vnější ovládací prvek

Přímé řízení životního cyklu hostitelů lze dosáhnout pomocí metody, které je možné volat externě:

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

Aplikace ASP.NET Core, konfigurace a spouštění hostitele. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.

Tento článek popisuje obecný hostitele ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), který se používá pro aplikace, které není zpracovávají požadavky HTTP.

Účelem obecný hostitele je oddělit kanálu HTTP z hostitele webového rozhraní API umožňující širší škálu scénářů hostitele. Zasílání zpráv, úlohy na pozadí a další úlohy jiným protokolem než HTTP podle obecného hostitele je výhodné společné funkce, jako jsou konfigurace, injektáž závislostí (DI) a protokolování.

Obecný hostitele je nového v ASP.NET Core 2.1 a není vhodné pro scénáře hostování webů. Pro web scénářích hostování, použijte [webového hostitele](xref:fundamentals/host/web-host). Obecný hostitele, nahradí webového hostitele v budoucí verzi a fungovat jako primární hostitele rozhraní API scénáře jiným protokolem než HTTP a protokolu HTTP.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([stažení](xref:index#how-to-download-a-sample))

Při spuštění ukázkové aplikace [Visual Studio Code](https://code.visualstudio.com/), použijte *externí nebo integrovaného terminálu*. Nejdou spustit v ukázce `internalConsole`.

Nastavení konzoly ve Visual Studio Code:

1. Otevřít *.vscode/launch.json* souboru.
1. V **.NET Core spuštění (konzola)** konfigurace, vyhledejte **konzoly** položka. Nastavte hodnotu na buď `externalTerminal` nebo `integratedTerminal`.

## <a name="introduction"></a>Úvod

Je k dispozici v knihovně obecný hostitele <xref:Microsoft.Extensions.Hosting> obor názvů a poskytuje [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) balíčku. `Microsoft.Extensions.Hosting` Je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).

<xref:Microsoft.Extensions.Hosting.IHostedService> je vstupním bodem k provádění kódu. Každý <xref:Microsoft.Extensions.Hosting.IHostedService> implementace provádí v pořadí podle [službu registrace v ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> je volána v každé <xref:Microsoft.Extensions.Hosting.IHostedService> při spuštění hostitele a <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> je volána v pořadí reverzní registrace při řádné vypnutí hostitele.

## <a name="set-up-a-host"></a>Nastavení hostitele

<xref:Microsoft.Extensions.Hosting.IHostBuilder> je hlavní komponenty, knihovny a aplikace použít k inicializaci, vytvoření a spuštění hostitele:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Možnosti

<xref:Microsoft.Extensions.Hosting.HostOptions> Konfigurace možností <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Časový limit vypnutí

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> Nastaví časový limit pro <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>. Výchozí hodnota je pět sekund.

Následující možnost konfigurace v `Program.Main` zvyšuje výchozí pět druhý vypnutí časový limit na 20 sekund:

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

Konfigurace hostitele bylo vytvořeno.

* Volání metody rozšíření u <xref:Microsoft.Extensions.Hosting.IHostBuilder> nastavit [obsahu kořenové](#content-root) a [prostředí](#environment).
* Čtení konfigurace od poskytovatelů konfigurace v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.

### <a name="extension-methods"></a>Rozšiřující metody

### <a name="application-key-name"></a>Klíč aplikace (název)

[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) vlastnost nastaven z konfigurace hostitele během vytváření hostitele. Pokud chcete explicitně nastavit hodnotu, použijte [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Klíč**: applicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení obsahujícího položku aplikaci přejděte.  
**Sada s použitím**: `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Proměnná prostředí**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))

### <a name="content-root"></a>Kořenový adresář obsahu

Toto nastavení určuje, kde začíná hostitele vyhledávání obsahu souborů.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Výchozí hodnota je do složky, ve které se nachází sestavení aplikace.  
**Sada s použitím**: `UseContentRoot`  
**Proměnná prostředí**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))

Pokud cesta neexistuje, hostitel se nepodaří spustit.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a>Prostředí

Nastaví aplikace [prostředí](xref:fundamentals/environments).

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: Produkční  
**Sada s použitím**: `UseEnvironment`  
**Proměnná prostředí**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` je [volitelné a uživatelem definovanými](#configurehostconfiguration))

Prostředí můžete nastavit na libovolnou hodnotu. Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`. Hodnoty se velká a malá písmena.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro hostitele. Konfigurace hostitele slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> pro použití v procesu sestavení aplikace.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> můžete volat vícekrát s přičítáním výsledky. Hostitel používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč.

Žádní poskytovatelé jsou zahrnuté ve výchozím nastavení. Je nutné explicitně zadat jakýkoli poskytovatelé konfigurace aplikace vyžaduje v <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, včetně:

* Konfigurace rozhraní File (např. z *hostsettings.json* souboru).
* Konfigurace proměnných prostředí.
* Konfigurace argument příkazového řádku.
* Žádné další požadované konfigurace poskytovatele.

Je povolená souboru konfigurace hostitele tak, že zadáte základní cesty aplikace s `SetBasePath` následovanou voláním do jednoho z [souboru poskytovatelé konfigurace](xref:fundamentals/configuration/index#file-configuration-provider). Ukázková aplikace používá soubor JSON, *hostsettings.json*a volání <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> využívat v souboru nastavení konfigurace hostitele.

Chcete-li přidat [konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hostitele, volání <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na tvůrce hostitele. `AddEnvironmentVariables` přijímá volitelný uživatelsky definovanou předponu. Ukázková aplikace používá předponou `PREFIX_`. Předpona, která se odebere, když jsou proměnné prostředí načteny. Když je ukázková aplikace hostitel nakonfigurovaný, hodnotu proměnné prostředí pro `PREFIX_ENVIRONMENT` stane hodnota konfigurace hostitele `environment` klíč.

Během vývoje. při použití [sady Visual Studio](https://visualstudio.microsoft.com) nebo spuštěním aplikace s `dotnet run`, lze nastavit proměnné prostředí *Properties/launchSettings.json* souboru. V [Visual Studio Code](https://code.visualstudio.com/), lze nastavit proměnné prostředí *.vscode/launch.json* souboru během vývoje. Další informace naleznete v tématu <xref:fundamentals/environments>.

[Příkazový řádek konfigurace](xref:fundamentals/configuration/index#command-line-configuration-provider) je přidána voláním <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>. Konfigurace příkazového řádku je tak, aby povolovala argumenty příkazového řádku k přepsání konfigurace poskytované starší poskytovatelé konfigurace přidáni jako poslední.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Je možné poskytnout další konfigurace [applicationName](#application-key-name) a [contentRoot](#content-root) klíče.

Příklad `HostBuilder` konfiguraci pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Konfigurace aplikace je vytvořen zavoláním <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementace. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> používá <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> k vytvoření <xref:Microsoft.Extensions.Configuration.IConfiguration> pro aplikaci. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> můžete volat vícekrát s přičítáním výsledky. Aplikace používá hodnotu kterékoli z těchto možností poslední nastaví pro daný klíč. Konfigurace vytvořil <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> je k dispozici na [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) pro následné operace a v <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

Konfigurace aplikace automaticky obdrží konfigurace hostitele poskytované [ConfigureHostConfiguration](#configurehostconfiguration).

Příklad aplikace konfigurace pomocí <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Přesunutí souborů nastavení do výstupního adresáře, zadejte soubory nastavení jako [položky projektu nástroje MSBuild](/visualstudio/msbuild/common-msbuild-project-items) v souboru projektu. Ukázková aplikace přesune své soubory JSON aplikace nastavení a *hostsettings.json* následujícím `<Content>` položky:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozšíření konfigurace, jako například <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> vyžadují další balíčky NuGet, jako například [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) a [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Pokud aplikace používá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), tyto balíčky musí být přidán do projektu kromě základní [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) balíčku. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>ConfigureServices

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> Přidá do aplikace služeb [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> můžete volat vícekrát s přičítáním výsledky.

Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.

[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá `AddHostedService` metodu rozšíření k přidání služby pro události doby života `LifetimeEventsHostedService`a úlohu na pozadí vypršel časový limit `TimedHostedService`, do aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> přidá delegáta pro konfiguraci zadaných <xref:Microsoft.Extensions.Logging.ILoggingBuilder>. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> může být volána více než jednou s přičítáním výsledky.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM a volání <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> zahájíte proces vypnutí. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokuje rozšíření, jako [RunAsync](#runasync) a [WaitForShutdownAsync](#waitforshutdownasync). <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> je zaregistrované předem. jako výchozí implementace životnost. Poslední doba života zaregistrovaný se používá.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfigurace kontejneru

Pro podporu připojení v ostatních kontejnerech, může přijmout hostitele <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. Poskytuje objekt pro vytváření, které nejsou součástí registrace kontejnerů DI, ale místo toho je vnitřní hostitel používá k vytvoření kontejneru pro konkrétní DI. [UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) přepisuje výchozí objekt pro vytváření použitý k vytvoření poskytovatele služby app service.

Konfigurace vlastního kontejneru je spravován <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metody. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> poskytuje možnosti silného typu pro konfiguraci kontejneru nad základního hostitele rozhraní API. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> můžete volat vícekrát s přičítáním výsledky.

Vytvoření služby kontejneru pro aplikace:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Poskytuje objekt pro vytváření služby kontejneru:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Použijte objekt pro vytváření a konfigurace kontejneru vlastní služby pro aplikaci:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozšiřitelnost

Rozšiřitelnost hostitele se provádí pomocí metody rozšíření na <xref:Microsoft.Extensions.Hosting.IHostBuilder>. Následující příklad ukazuje, jak rozšiřující metoda rozšiřuje <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementaci [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) příklad jsme vám ukázali v <xref:fundamentals/host/hosted-services>.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Vytvoří aplikaci `UseHostedService` metodu rozšíření k registraci hostované služby předaný `T`:

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

## <a name="manage-the-host"></a>Spravovat hostitele

<xref:Microsoft.Extensions.Hosting.IHost> Implementace je zodpovědný za spouštění a zastavování <xref:Microsoft.Extensions.Hosting.IHostedService> implementace, které jsou registrovány v kontejneru služby.

### <a name="run"></a>Spustit

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> spuštění aplikace a blokuje volající vlákno, dokud nebude ukončen hostitele:

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> spuštění aplikace a vrátí <xref:System.Threading.Tasks.Task> , která se dokončí, když se aktivuje token zrušení nebo vypnutí:

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

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> umožňuje podporu konzoly, sestaví a spustí hostitele a čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM vypnout.

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

### <a name="start-and-stopasync"></a>Počáteční a StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> Hostitel spustí synchronně.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> pokusí se zastavit hostitele v rámci zadaného časového limitu.

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

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> spuštění aplikace.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> Ukončí aplikaci.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> se spouští přes <xref:Microsoft.Extensions.Hosting.IHostLifetime>, jako například <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (čeká na kombinaci kláves Ctrl + C/SIGINT nebo SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Vrátí <xref:System.Threading.Tasks.Task> , která se dokončí při vypnutí se spouští přes daný token a volání <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.

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

### <a name="external-control"></a>Vnější ovládací prvek

Vnější ovládací prvek hostitele lze dosáhnout pomocí metody, které je možné volat externě:

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

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> volá se na začátku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, která čeká, dokud neskončí než budete pokračovat. To umožňuje zpoždění spuštění, dokud signalizován externí událostí.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment rozhraní

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> poskytuje informace o aplikaci prvku hostitelské prostředí. Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> Chcete-li použít její vlastnosti a metody rozšíření:

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

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime rozhraní

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> umožňuje po spuštění a vypnutí aktivity, včetně žádostí o řádné vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci <xref:System.Action> metody, které definují události spuštění a vypnutí.

| Token zrušení | Při aktivaci&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Hostitel plně byla spuštěna. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Hostitel se dokončuje řádné vypnutí. By měl zpracovat všechny požadavky. Vypnutí blokuje, dokud se tato událost se dokončí. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Hostitel provádí řádné vypnutí. Žádosti se možná ještě zpracovávají. Vypnutí blokuje, dokud se tato událost se dokončí. |

Vložit konstruktoru <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service do libovolné třídy. [Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) používá konstruktor injektáž do `LifetimeEventsHostedService` třídy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementace) k registraci události.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> požádá o ukončení aplikace. Následující třídy používá <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> řádně vypnout aplikaci při třídy `Shutdown` volání metody:

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
