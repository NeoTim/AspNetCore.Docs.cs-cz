---
title: ASP.NET Core webového hostitele
author: guardrex
description: Přečtěte si o webovém hostiteli v ASP.NET Core, který zodpovídá za správu spouštění a životního cyklu aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: d387098662cc832cc0e49b6a1636f0ebcc7308de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081693"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core webového hostitele

Podle [Luke Latham](https://github.com/guardrex)

ASP.NET Core aplikace nakonfigurují a spouštějí *hostitele*. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Minimálně hostitel nakonfiguruje server a kanál zpracování požadavků. Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.

::: moniker range=">= aspnetcore-3.0"

Tento článek se zabývá webovým hostitelem, který zůstává dostupný jenom pro zpětnou kompatibilitu. [Obecný hostitel](xref:fundamentals/host/generic-host) je doporučený pro všechny typy aplikací.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Tento článek se zabývá webovým hostitelem, který je určený pro hostování webových aplikací. Pro jiné druhy aplikací použijte [obecného hostitele](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Nastavení hostitele

Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). To se obvykle provádí v vstupním bodě aplikace, `Main` metodě.

V šablonách `Main` projektů se nachází v *program.cs*. Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) , aby zahájila nastavování hostitele:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

Kód, který volá `CreateDefaultBuilder` , je v metodě s `CreateWebHostBuilder`názvem, která ho odděluje od kódu v `Main` volání `Run` objektu Builder. Toto oddělení se vyžaduje při použití [Entity Framework Corech nástrojů](/ef/core/miscellaneous/cli/). Nástroje očekávají `CreateWebHostBuilder` , že vyvolají metodu, kterou můžou zavolat v době návrhu, aby se nakonfiguroval hostitel bez spuštění aplikace. Alternativou je implementace `IDesignTimeDbContextFactory`. Další informace najdete v tématu [vytváření DbContext při návrhu](/ef/core/miscellaneous/cli/dbcontext-creation).

`CreateDefaultBuilder`provádí následující úlohy:

* Nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.
* Nastaví kořen obsahu na cestu vrácenou [adresářem. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Načte [konfiguraci hostitele](#host-configuration-values) z:
  * Proměnné prostředí s `ASPNETCORE_` předponou ( `ASPNETCORE_ENVIRONMENT`například).
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace v následujícím pořadí:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Konfiguruje [protokolování](xref:fundamentals/logging/index) pro konzolu a výstup ladění. Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v sekci Konfigurace protokolování souboru *appSettings. JSON* nebo *appSettings. { Environment}. JSON* soubor.
* Při spuštění za službou IIS `CreateDefaultBuilder` s [modulem ASP.NET Core](xref:host-and-deploy/aspnet-core-module)umožňuje [integraci služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace. Integrace služby IIS také nakonfiguruje aplikaci pro [zachycení chyb při spuštění](#capture-startup-errors). Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.
* Nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , pokud je prostředí aplikace vyvíjené. Další informace najdete v tématu [ověřování oboru](#scope-validation).

`CreateDefaultBuilder` Konfiguraci definovanou pomocí lze přepsat a rozšířit [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalšími metodami a metodami rozšíření [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Následuje několik příkladů:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá k určení dalších `IConfiguration` aplikací. Následující `ConfigureAppConfiguration` volání přidá delegáta pro zahrnutí konfigurace aplikace do souboru *appSettings. XML* . `ConfigureAppConfiguration`může být volána několikrát. Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí). Viz část [hodnoty konfigurace hostitele](#host-configuration-values) .

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Následující `ConfigureLogging` volání přidá delegáta pro konfiguraci minimální úrovně protokolování ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel). Toto nastavení přepisuje nastavení v *appSettings. Development. JSON* (`LogLevel.Debug`) a *appSettings. Produkční. JSON* (`LogLevel.Error`) nakonfigurovaný pomocí `CreateDefaultBuilder`. `ConfigureLogging`může být volána několikrát.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Následující volání `ConfigureKestrel` přepíše výchozí [omezení. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů zavedených při konfiguraci `CreateDefaultBuilder`Kestrel:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepisuje výchozí [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) z 30 000 000 bajtů zavedených při konfiguraci `CreateDefaultBuilder`Kestrel:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

*Kořen obsahu* určuje, kde hostitel vyhledává soubory obsahu, například soubory zobrazení MVC. Při spuštění aplikace z kořenové složky projektu se jako kořen obsahu použije kořenová složka projektu. Toto je výchozí použití v [aplikaci Visual Studio](https://visualstudio.microsoft.com) a [dotnet New Templates](/dotnet/core/tools/dotnet-new).

Další informace o konfiguraci aplikace najdete v tématu <xref:fundamentals/configuration/index>.

> [!NOTE]
> Jako alternativu k použití statické `CreateDefaultBuilder` metody je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2. x.

Při nastavování hostitele je možné zadat metody [Konfigurace](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) . `Configure` Pokud je `Startup` třída zadána, musí definovat metodu. Další informace naleznete v tématu <xref:fundamentals/startup>. Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání. Vícenásobné volání `Configure` nebo `UseStartup` v `WebHostBuilder` nastavení nahradit předchozí.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:

* Konfigurace tvůrce hostitele, což zahrnuje proměnné prostředí ve formátu `ASPNETCORE_{configurationKey}`. Například, `ASPNETCORE_ENVIRONMENT`.
* Rozšíření jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz oddíl pro [přepsání konfigurace](#override-configuration) ).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč. Při nastavení hodnoty s `UseSetting`hodnotou je hodnota nastavena jako řetězec bez ohledu na typ.

Hostitel používá jakoukoli možnost, která hodnotu nastaví jako poslední. Další informace najdete v tématu [přepsání konfigurace](#override-configuration) v následující části.

### <a name="application-key-name"></a>Klíč aplikace (název)

Vlastnost [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během vytváření hostitele. Hodnota je nastavená na název sestavení, které obsahuje vstupní bod aplikace. Chcete-li nastavit hodnotu explicitně, použijte [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

**Klíč**: ApplicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení, které obsahuje vstupní bod aplikace  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Zachytit chyby při spuštění

Toto nastavení řídí zachycení chyb při spuštění.

**Klíč**: captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: Použije se výchozí hodnota, `true` PokudseaplikacenespustísKestrelzaIIS,kdejevýchozíhodnota.`false`  
**Nastavit pomocí**:`CaptureStartupErrors`  
**Proměnná prostředí**:`ASPNETCORE_CAPTURESTARTUPERRORS`

V `false`důsledku dojde k chybám při spuštění hostitele. Když `true`hostitel zachytí výjimky během spouštění a pokusí se o spuštění serveru.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Kořen obsahu

Toto nastavení určuje, kde ASP.NET Core začít hledat soubory obsahu, například zobrazení MVC. 

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Výchozím nastavením je složka, ve které se nachází sestavení aplikace.  
**Nastavit pomocí**:`UseContentRoot`  
**Proměnná prostředí**:`ASPNETCORE_CONTENTROOT`

Kořen obsahu se používá také jako základní cesta pro [Nastavení kořenového adresáře webu](#web-root). Pokud cesta neexistuje, hostitele se nepodaří spustit.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a>Podrobné chyby

Určuje, zda mají být zachyceny podrobné chyby.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_DETAILEDERRORS`

Když je tato možnost povolená <a href="#environment"></a> (nebo když je `Development`prostředí nastavené na), aplikace zachycuje podrobné výjimky.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Prostředí

Nastaví prostředí aplikace.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: Produkční  
**Nastavit pomocí**:`UseEnvironment`  
**Proměnná prostředí**:`ASPNETCORE_ENVIRONMENT`

Prostředí lze nastavit na libovolnou hodnotu. Hodnoty definované rozhraním zahrnují `Development`, `Staging`a `Production`. U hodnot se nerozlišují malá a velká písmena. Ve výchozím nastavení je *prostředí* načteno z `ASPNETCORE_ENVIRONMENT` proměnné prostředí. Při použití sady [Visual Studio](https://visualstudio.microsoft.com)mohou být proměnné prostředí nastaveny v souboru *launchSettings. JSON* . Další informace naleznete v tématu <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hostování spouštěcích sestavení

Nastaví hostování spouštěcích sestavení aplikace.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají načíst při spuštění.

I když je hodnota konfigurace výchozím prázdným řetězcem, hostující spouštěcí sestavení vždy zahrnuje sestavení aplikace. Po zadání hostování spouštěcích sestavení jsou přidána do sestavení aplikace pro načtení, když aplikace během spouštění sestaví své běžné služby.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>Port HTTPS

Nastavte port přesměrování HTTPS. Používá se při [vynucování https](xref:security/enforcing-ssl).

**Klíč**: https_port **typ**:**Výchozí hodnota** *řetězce*
: Výchozí hodnota není nastavena.
**Nastavit pomocí**: `UseSetting`
**Proměnná prostředí**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hostování nespouštěcích sestavení pro vyloučení

Středníkem oddělený řetězec hostujících spouštěcích sestavení, která se mají vyloučit při spuštění.

**Klíč**: hostingStartupExcludeAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferovat hostování adres URL

Určuje, zda má hostitel naslouchat adresám URL nakonfigurovaným `WebHostBuilder` pomocí a místo nakonfigurovaných `IServer` s implementací.

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Nastavit pomocí**:`PreferHostingUrls`  
**Proměnná prostředí**:`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Zabránit spuštění hostování

Zabraňuje automatickému načítání hostujících spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**: preventHostingStartup  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Adresy URL serveru

Označuje IP adresy nebo adresy hostitelů s porty a protokoly, na kterých má Server naslouchat požadavky.

**Klíč**: adresy URL  
**Typ**: *řetězec*  
**Výchozí**: http://localhost:5000  
**Nastavit pomocí**:`UseUrls`  
**Proměnná prostředí**:`ASPNETCORE_URLS`

Nastavte na středníkem oddělený (;) Seznam předpon adres URL, na které má server odpovědět Například, `http://localhost:123`. Pomocí příkazu\*"" určete, že server má naslouchat žádostem na jakékoli IP adrese nebo názvu hostitele pomocí zadaného portu a protokolu ( `http://*:5000`například). Protokol (`http://` nebo `https://`) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel má své vlastní rozhraní API pro konfiguraci koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Časový limit vypnutí

Určuje dobu, po kterou se má čekat na vypnutí webového hostitele.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: 5  
**Nastavit pomocí**:`UseShutdownTimeout`  
**Proměnná prostředí**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

I když klíč přijímá *int* `UseSetting` s `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`(například), metoda rozšíření [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) přebírá [časový interval](/dotnet/api/system.timespan).

Po uplynutí časového limitu hostování:

* Spustí [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb a protokolování všech chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví, jakmile se aplikace vypíná. Služby se zastavují i v případě, že se nedokončily zpracování. Pokud služby vyžadují ukončení déle, zvyšte časový limit.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Spouštěcí sestavení

Určuje sestavení, ve kterém má být `Startup` vyhledána třída.

**Klíč**: startupAssembly  
**Typ**: *řetězec*  
**Výchozí**: Sestavení aplikace  
**Nastavit pomocí**:`UseStartup`  
**Proměnná prostředí**:`ASPNETCORE_STARTUPASSEMBLY`

Lze odkazovat na sestavení podle`string`názvu () nebo`TStartup`typu (). Pokud je `UseStartup` voláno více metod, má poslední z nich přednost.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Webový kořenový adresář

Nastaví relativní cestu k statickým assetům aplikace.

**Klíč**: Webroot  
**Typ**: *řetězec*  
**Výchozí**: Pokud tento parametr nezadáte, výchozí hodnota je "(kořen obsahu)/wwwroot", pokud cesta existuje. Pokud cesta neexistuje, použije se zprostředkovatel souboru no-op.  
**Nastavit pomocí**:`UseWebRoot`  
**Proměnná prostředí**:`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a>Přepsat konfiguraci

Pro konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) . V následujícím příkladu je konfigurace hostitele volitelně určena v souboru *HostSettings. JSON* . Jakékoli konfigurace načtené ze souboru *HostSettings. JSON* může přepsat argumenty příkazového řádku. Vytvořená konfigurace (v `config`) se používá ke konfiguraci hostitele pomocí [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder`konfigurace je přidána do konfigurace aplikace, ale tato konverzace není pravdivá&mdash; `ConfigureAppConfiguration` , nemá vliv na `IWebHostBuilder` konfiguraci.

Přepsání konfigurace, kterou `UseUrls` poskytuje, pomocí konfigurace *HostSettings. JSON* First, druhé konfigurace argumentů příkazového řádku:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> Metoda rozšíření [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) momentálně není schopná analyzovat konfigurační oddíl, který vrácený `GetSection` `.UseConfiguration(Configuration.GetSection("section"))`(například. Metoda filtruje konfigurační klíče na požadovanou část, ale ponechá název oddílu na klíčích ( `section:urls`například, `section:environment`). `GetSection` Metoda očekává, že klíče budou `WebHostBuilder` odpovídat `urls`klíčům (například, `environment`). `UseConfiguration` Přítomnost názvu oddílu u klíčů brání hodnotám oddílu v konfiguraci hostitele. Tento problém bude vyřešen v příští verzi. Další informace a alternativní řešení naleznete v [části předání konfiguračního oddílu do WebHostBuilder. UseConfiguration používá úplné klíče](https://github.com/aspnet/Hosting/issues/839).
>
> `UseConfiguration`pouze kopíruje klíče ze zadaného `IConfiguration` do konfigurace tvůrce hostitele. Nastavení `reloadOnChange: true` souborů JSON, ini a XML nastavení proto nemá žádný vliv.

Chcete-li určit spuštění hostitele na konkrétní adrese URL, lze požadovanou hodnotu předat z příkazového řádku při [spuštění příkazu dotnet](/dotnet/core/tools/dotnet-run). Argument příkazového řádku Přepisuje `urls` hodnotu ze souboru *HostSettings. JSON* a server naslouchá na portu 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Správa hostitele

**Spuštění**

`Run` Metoda spustí webovou aplikaci a zablokuje volající vlákno, dokud nebude hostitel vypnutý:

```csharp
host.Run();
```

**Start**

Spusťte hostitele neblokujícím způsobem voláním jeho `Start` metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Pokud se do `Start` metody předává seznam adres URL, naslouchá na zadaných adresách URL:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

Aplikace může inicializovat a spustit nového hostitele s použitím předem nakonfigurovaných výchozích hodnot pomocí statické `CreateDefaultBuilder` metody pro usnadnění. Tyto metody spustí server bez výstupu konzoly a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekají na přerušení (CTRL-C/SIGINT nebo SIGTERM):

**Spustit (aplikace RequestDelegate)**

Začněte s `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

V prohlížeči `http://localhost:5000` vytvořte žádost, aby obdržela odpověď "Hello World!". `WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.

**Spustit (adresa URL řetězce, aplikace v RequestDelegate)**

Začněte s adresou URL a `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **Start (aplikace RequestDelegate)** , s výjimkou, že aplikace `http://localhost:8080`reaguje.

**Spustit (akce&lt;IRouteBuilder&gt; routeBuilder)**

Pro použití middlewaru `IRouteBuilder` směrování použijte instanci ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)):

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Použijte následující požadavky prohlížeče s příkladem:

| Request                                    | Odpověď                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Novák!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos Dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Vyvolá výjimku s řetězcem "Ooops!" |
| `http://localhost:5000/throw`              | Vyvolá výjimku s řetězcem "uh Oh!". |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Ahoj světe!                             |

`WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.

**Spustit (adresa URL řetězce,&lt;Action&gt; IRouteBuilder routeBuilder)**

Použijte adresu URL a instanci `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **Start (Action&lt;&gt; IRouteBuilder routeBuilder)** , s výjimkou, že aplikace `http://localhost:8080`reaguje na.

**StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**

Zadejte delegáta pro konfiguraci `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

V prohlížeči `http://localhost:5000` vytvořte žádost, aby obdržela odpověď "Hello World!". `WaitForShutdown`blokuje až do vystavení přerušení (CTRL-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a počká, až se stisknutí klávesy ukončí.

**StartWith (adresa URL řetězce,&lt;akce&gt; aplikace IApplicationBuilder)**

Zadejte adresu URL a delegáta pro konfiguraci `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **StartWith (aplikace Action&lt;IApplicationBuilder&gt; )** , s výjimkou, že aplikace `http://localhost:8080`reaguje.

## <a name="ihostingenvironment-interface"></a>Rozhraní IHostingEnvironment

[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o prostředí hostování webu aplikace. Použijte [Injektáže konstruktoru](xref:fundamentals/dependency-injection) pro získání, `IHostingEnvironment` aby bylo možné použít jeho vlastnosti a metody rozšíření:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[Přístup založený na konvenci](xref:fundamentals/environments#environment-based-startup-class-and-methods) je možné použít ke konfiguraci aplikace při spuštění na základě prostředí. Alternativně můžete vložit `IHostingEnvironment` `Startup` do konstruktoru konstruktor pro použití v `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> `IsDevelopment` Kromě metody rozšíření, `IHostingEnvironment` nabídky `IsStaging`, `IsProduction`a metody.`IsEnvironment(string environmentName)` Další informace naleznete v tématu <xref:fundamentals/environments>.

Službu `IHostingEnvironment` je také možné vložit přímo `Configure` do metody nastavení kanálu zpracování:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment`dá se vložit do `Invoke` metody při vytváření vlastního [middlewaru](xref:fundamentals/middleware/write):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>Rozhraní IApplicationLifetime

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí. Tři vlastnosti rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.

| Token zrušení    | Aktivováno, když&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Hostitel byl plně spuštěn. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Hostitel dokončuje řádné vypnutí. Všechny požadavky by se měly zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Hostitel provádí bezproblémové vypnutí. Žádosti se pořád dají zpracovat. Bloky vypnutí, dokud se tato událost nedokončí. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) žádá o ukončení aplikace. Následující třída používá `StopApplication` k bezproblémovému vypnutí aplikace při volání `Shutdown` metody třídy:

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

## <a name="scope-validation"></a>Ověření rámce životnosti

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , pokud je prostředí aplikace vyvíjené.

Když `ValidateScopes` je nastavená `true`na, výchozí poskytovatel služby provede kontrolu, aby ověřil, že:

* Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.
* Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.

Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider). Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.

Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil. Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace. Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.

Pokud chcete vždy ověřit obory, včetně v produkčním prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) pomocí [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrci hostitele:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
