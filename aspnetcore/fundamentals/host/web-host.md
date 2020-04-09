---
title: ASP.NET základní webový hostitel
author: rick-anderson
description: Další informace o webovém hostiteli v ASP.NET Core, který je zodpovědný za spuštění aplikace a správu životnosti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666710"
---
# <a name="aspnet-core-web-host"></a>ASP.NET základní webový hostitel

ASP.NET aplikace Core nakonfigurují a spouštějí *hostitele*. Hostitel je zodpovědný za spuštění aplikace a správu životnosti. Hostitel minimálně konfiguruje server a kanál zpracování požadavků. Hostitel může také nastavit protokolování, vkládání závislostí a konfiguraci.

::: moniker range=">= aspnetcore-3.0"

Tento článek popisuje webový hostitel, který zůstává k dispozici pouze pro zpětnou kompatibilitu. [Obecný hostitel](xref:fundamentals/host/generic-host) se doporučuje pro všechny typy aplikací.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek popisuje webhosting, který je určen pro hostování webových aplikací. Pro jiné druhy aplikací použijte [obecný hostitel](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Nastavení hostitele

Vytvořte hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). To se obvykle provádí v vstupním bodě `Main` aplikace, metoda.

V šablonách projektu `Main` se nachází v *Program.cs*. Typická aplikace volá [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ke spuštění nastavení hostitele:

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

Kód, který `CreateDefaultBuilder` volá je `CreateWebHostBuilder`v metodě s názvem `Main` , `Run` která jej odděluje od kódu v tomto volání objektu tvůrce. Toto oddělení je vyžadováno, pokud používáte [nástroje Core framework .](/ef/core/miscellaneous/cli/) Nástroje očekávají, že `CreateWebHostBuilder` najdou metodu, kterou mohou volat v době návrhu ke konfiguraci hostitele bez spuštění aplikace. Alternativou je `IDesignTimeDbContextFactory`implementace . Další informace naleznete v [tématu Návrh-time DbContext Vytvoření](/ef/core/miscellaneous/cli/dbcontext-creation).

`CreateDefaultBuilder` provede následující úlohy:

* Konfiguruje [kestrelserver](xref:fundamentals/servers/kestrel) jako webový server pomocí poskytovatelů konfigurace hostování aplikace. Výchozí možnosti serveru Kestrel naleznete <xref:fundamentals/servers/kestrel#kestrel-options>v tématu .
* Nastaví [kořenový obsah](xref:fundamentals/index#content-root) na cestu vrácenou [directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Načte [konfiguraci hostitele](#host-configuration-values) z:
  * Proměnné prostředí s `ASPNETCORE_` předponou `ASPNETCORE_ENVIRONMENT`(například ).
  * Argumenty příkazového řádku.
* Načte konfiguraci aplikace v následujícím pořadí od:
  * *appsettings.json*.
  * *nastavení aplikace. {Prostředí}.json*.
  * [Správce tajných barev](xref:security/app-secrets) při `Development` spuštění aplikace v prostředí pomocí sestavení položky.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Konfiguruje [protokolování](xref:fundamentals/logging/index) pro výstup konzoly a ladění. Protokolování zahrnuje pravidla [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) zadaná v části Konfigurace protokolování *souboru appsettings.json* nebo *appsettings.{ Prostředí}.json.*
* Při spuštění za službou IIS `CreateDefaultBuilder` pomocí [ASP.NET základního modulu](xref:host-and-deploy/aspnet-core-module)umožňuje integraci [služby IIS](xref:host-and-deploy/iis/index), která konfiguruje základní adresu a port aplikace. Integrace služby IIS také konfiguruje aplikaci tak, aby [zaznamenávala chyby při spuštění](#capture-startup-errors). Výchozí možnosti iis <xref:host-and-deploy/iis/index#iis-options>naleznete v tématu .
* Nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` v případě, že prostředí aplikace je vývoj. Další informace naleznete v [tématu Ověření oboru](#scope-validation).

Konfigurace definovaná `CreateDefaultBuilder` hodnotou může být přepsána a rozšířena [pomocí ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a dalších metod a rozšiřujících metod [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Několik příkladů následuje:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) se používá `IConfiguration` k určení další pro aplikaci. Následující `ConfigureAppConfiguration` volání přidá delegáta, aby zahrnul konfiguraci aplikace do souboru *appsettings.xml.* `ConfigureAppConfiguration`může být volána vícekrát. Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí). Viz část Hodnoty [konfigurace hostitele.](#host-configuration-values)

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Následující `ConfigureLogging` volání přidá delegáta ke konfiguraci minimální úrovně protokolování[(SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel). Toto nastavení přepíše nastavení v *nastavení aplikace. Development.json* `LogLevel.Debug`( ) a *nastavení aplikací. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`nakonfigurovaný . `ConfigureLogging`může být volána vícekrát.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Následující volání `ConfigureKestrel` přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtů vytvořených při `CreateDefaultBuilder`konfiguraci Kestrel pomocí :

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtů vytvořených při `CreateDefaultBuilder`kestrel byl nakonfigurován :

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

[Kořenový adresář obsahu](xref:fundamentals/index#content-root) určuje, kde hostitel hledá soubory obsahu, například soubory zobrazení MVC. Při spuštění aplikace z kořenové složky projektu se jako kořenový obsah použije kořenová složka projektu. Toto je výchozí nastavení používané v [sadě Visual Studio](https://visualstudio.microsoft.com) a nové šablony [dotnet](/dotnet/core/tools/dotnet-new).

Další informace o konfiguraci <xref:fundamentals/configuration/index>aplikace najdete v tématu .

> [!NOTE]
> Jako alternativu k `CreateDefaultBuilder` použití statické metody je vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) podporovaným přístupem s ASP.NET Core 2.x.

Při nastavování hostitele lze poskytnout metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) a [ConfigureServices.](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) Pokud `Startup` je zadána třída, `Configure` musí definovat metodu. Další informace naleznete v tématu <xref:fundamentals/startup>. Více volání `ConfigureServices` k sobě navzájem. Více volání `Configure` `UseStartup` nebo `WebHostBuilder` na nahradit předchozí nastavení.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) spoléhá na následující přístupy k nastavení hodnot konfigurace hostitele:

* Konfigurace tvůrce hostitelů, která zahrnuje `ASPNETCORE_{configurationKey}`proměnné prostředí s formátem . Například, `ASPNETCORE_ENVIRONMENT`.
* Rozšíření, jako je [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz [přepsání konfigurace](#override-configuration) části).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč. Při nastavování `UseSetting`hodnoty s , hodnota je nastavena jako řetězec bez ohledu na typ.

Hostitel používá podle toho, která možnost nastaví hodnotu jako poslední. Další informace naleznete v tématu [Přepsat konfiguraci](#override-configuration) v další části.

### <a name="application-key-name"></a>Aplikační klíč (název)

::: moniker range=">= aspnetcore-3.0"

Vlastnost `IWebHostEnvironment.ApplicationName` je automaticky nastavena při [usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) je volána během výstavby hostitele. Hodnota je nastavena na název sestavení obsahující vstupní bod aplikace. Chcete-li hodnotu nastavit explicitně, použijte [webHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Vlastnost [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) je automaticky nastavena při volání [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) během výstavby hostitele. Hodnota je nastavena na název sestavení obsahující vstupní bod aplikace. Chcete-li hodnotu nastavit explicitně, použijte [webHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

**Klíč**: applicationName  
**Typ**: *řetězec*  
**Výchozí**: Název sestavení obsahujícího vstupní bod aplikace.  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Zachytit chyby při spuštění

Toto nastavení řídí zachycení chyb při spuštění.

**Klíč:** captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: Výchozí `false` nastavení, pokud aplikace běží s Kestrel `true`za IIS, kde je výchozí .  
**Nastavit pomocí**:`CaptureStartupErrors`  
**Proměnná prostředí**:`ASPNETCORE_CAPTURESTARTUPERRORS`

Když `false`, chyby při spuštění za následek ukončení hostitele. Když `true`hostitel zachytí výjimky při spuštění a pokusí se spustit server.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Kořen obsahu

Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu.

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: Výchozí je složka, ve které se nachází sestavení aplikace.  
**Nastavit pomocí**:`UseContentRoot`  
**Proměnná prostředí**:`ASPNETCORE_CONTENTROOT`

Kořen obsahu se také používá jako základní cesta pro [kořenový adresář webu](xref:fundamentals/index#web-root). Pokud kořenová cesta obsahu neexistuje, hostitel se nepodaří spustit.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Další informace naleznete v tématu:

* [Základy: Kořen obsahu](xref:fundamentals/index#content-root)
* [Kořenový adresář webu](#web-root)

### <a name="detailed-errors"></a>Podrobné chyby

Určuje, zda by měly být zachyceny podrobné chyby.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_DETAILEDERRORS`

Pokud je povoleno (nebo pokud `Development`je <a href="#environment">nastaveno</a> prostředí ), aplikace zachytí podrobné výjimky.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Prostředí

Nastaví prostředí aplikace.

**Klíč**: životní prostředí  
**Typ**: *řetězec*  
**Výchozí**: Výroba  
**Nastavit pomocí**:`UseEnvironment`  
**Proměnná prostředí**:`ASPNETCORE_ENVIRONMENT`

Prostředí lze nastavit na libovolnou hodnotu. Mezi hodnoty definované `Development` `Staging`rámcem `Production`patří , , a . Hodnoty nerozlišují malá a velká písmena. Ve výchozím nastavení *prostředí* je `ASPNETCORE_ENVIRONMENT` číst z proměnné prostředí. Při použití [sady Visual Studio](https://visualstudio.microsoft.com)mohou být v souboru *launchSettings.json* nastaveny proměnné prostředí. Další informace naleznete v tématu <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hostování spouštěcích sestavení

Nastaví hostitelská sestavení aplikace pro spuštění.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který se načte při spuštění.

Přestože hodnota konfigurace výchozí prázdný řetězec, hostování spouštěcí sestavení vždy součástí sestavení aplikace. Když jsou k dispozici hostování spouštěcísestavení, jsou přidány do sestavení aplikace pro načtení při aplikaci vytváří své běžné služby při spuštění.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>HTTPS Port

Nastavte port přesměrování HTTPS. Používá se při [vynucování protokolu HTTPS](xref:security/enforcing-ssl).

**Klíč**: https_port **Typ**: *řetězec*
**Výchozí**: Výchozí hodnota není nastavena.
**Nastavit**pomocí `UseSetting` 
: **Proměnná prostředí**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hostování sestavení vyloučení po spuštění

Řetězec pro hostování spouštěcích sestavení oddělených středníkem, který má být při spuštění vyloučen.

**Klíč**: hostingStartupExcludeAssemblies  
**Typ**: *řetězec*  
**Výchozí**: Prázdný řetězec  
**Nastavit pomocí**:`UseSetting`  
**Proměnná prostředí**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferovat adresy URL hostování

Označuje, zda má hostitel naslouchat adresám `WebHostBuilder` URL nakonfigurovaným s namísto adres nakonfigurovaných s implementací. `IServer`

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Nastavit pomocí**:`PreferHostingUrls`  
**Proměnná prostředí**:`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Zabránit spuštění hostingu

Zabraňuje automatickému načítání hostitelských spouštěcích sestavení, včetně hostování spouštěcích sestavení nakonfigurovaných sestavením aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

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

Označuje adresy IP nebo hostitelské adresy s porty a protokoly, které by měl server naslouchat požadavkům.

**Klíč**: urls  
**Typ**: *řetězec*  
**Výchozí :**http://localhost:5000  
**Nastavit pomocí**:`UseUrls`  
**Proměnná prostředí**:`ASPNETCORE_URLS`

Nastaveno na středník-oddělené (;) seznam předpon URL, na které by měl server reagovat. Například, `http://localhost:123`. Pomocí\*" " označuje, že server by měl naslouchat požadavkům na libovolnou adresu IP `http://*:5000`nebo název hostitele pomocí zadaného portu a protokolu (například ). Protokol (`http://` `https://`nebo ) musí být součástí každé adresy URL. Podporované formáty se mezi servery liší.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel má vlastní konfigurační rozhraní API koncového bodu. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Časový čas vypnutí

Určuje dobu čekání na vypnutí webového hostitele.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: 5  
**Nastavit pomocí**:`UseShutdownTimeout`  
**Proměnná prostředí**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Přestože klíč přijímá *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension metoda trvá [TimeSpan](/dotnet/api/system.timespan).

Během časového období, hosting:

* Aktivuje [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Pokusí se zastavit hostované služby, protokolování všechny chyby pro služby, které se nepodaří zastavit.

Pokud vyprší časový limit před zastavením všech hostovaných služeb, všechny zbývající aktivní služby se zastaví při vypnutí aplikace. Služby se zastaví, i když ještě nedokončily zpracování. Pokud služby vyžadují další čas k zastavení, zvyšte časový čas.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Sestavení při spuštění

Určuje sestavení k hledání `Startup` třídy.

**Klíč**: startupAssembly  
**Typ**: *řetězec*  
**Výchozí**: Sestavení aplikace  
**Nastavit pomocí**:`UseStartup`  
**Proměnná prostředí**:`ASPNETCORE_STARTUPASSEMBLY`

Na sestavení podle`string`názvu (`TStartup`) nebo typu ( ) lze odkazovat. Pokud `UseStartup` je voláno více metod, má přednost poslední metoda.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Kořenový adresář webu

Nastaví relativní cestu ke statickým datovým zdrojům aplikace.

**Klíč**: webroot  
**Typ**: *řetězec*  
**Výchozí**: Výchozí `wwwroot`hodnota je . Cesta k *kořenovému adresáři obsahu}/www root* musí existovat. Pokud cesta neexistuje, použije se zprostředkovatel souboru bez operace.  
**Nastavit pomocí**:`UseWebRoot`  
**Proměnná prostředí**:`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Další informace naleznete v tématu:

* [Základy: Kořen webu](xref:fundamentals/index#web-root)
* [Kořen obsahu](#content-root)

## <a name="override-configuration"></a>Přepsat konfiguraci

Ke konfiguraci webového hostitele použijte [konfiguraci](xref:fundamentals/configuration/index) konfigurace. V následujícím příkladu je konfigurace hostitele volitelně zadána v souboru *hostsettings.json.* Jakákoli konfigurace načtená ze souboru *hostsettings.json* může být přepsána argumenty příkazového řádku. Vytvořená konfigurace `config`(in) se používá ke konfiguraci hostitele pomocí [useConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder`konfigurace je přidána do konfigurace aplikace, ale opak&mdash; `ConfigureAppConfiguration` není pravdivý `IWebHostBuilder` nemá vliv na konfiguraci.

Přepsání konfigurace poskytované `UseUrls` pomocí konfigurace *hostsettings.json* first, příkazový řádek argument config druhý:

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
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) pouze kopíruje `IConfiguration` klíče z poskytnutých do konfigurace tvůrce hostitele. Proto nastavení `reloadOnChange: true` pro soubory nastavení JSON, INI a XML nemá žádný vliv.

Chcete-li určit spuštění hostitele na konkrétní adrese URL, požadovaná hodnota může být předána z příkazového řádku při provádění [spuštění dotnet](/dotnet/core/tools/dotnet-run). Argument příkazového řádku přepíše hodnotu `urls` ze souboru *hostsettings.json* a server naslouchá na portu 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Správa hostitele

**Spustit**

Metoda `Run` spustí webovou aplikaci a zablokuje volající vlákno, dokud se hostitel nevypne:

```csharp
host.Run();
```

**Zahájení**

Spusťte hostitele neblokujícím způsobem `Start` voláním jeho metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Pokud je `Start` metodě předán seznam adres URL, naslouchá na zadaných adresách URL:

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

Aplikace může inicializovat a spustit nového hostitele pomocí `CreateDefaultBuilder` předem nakonfigurovaných výchozích hodnot pomocí statické metody pohodlí. Tyto metody spustit server bez výstupu konzoly a [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) čekat na přestávku (Ctrl-C/SIGINT nebo SIGTERM):

**Start (aplikace RequestDelegate)**

Začněte `RequestDelegate`s :

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Požádejte v prohlížeči `http://localhost:5000` o odpověď "Hello World!" `WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.

**Start (adresa url řetězce, aplikace RequestDelegate)**

Začněte s `RequestDelegate`adresou URL a :

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **Start(RequestDelegate app)**, s `http://localhost:8080`výjimkou aplikace reaguje na .

**Start (akce\<IRouteBuilder> routeBuilder)**

Použijte instanci `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) k použití middlewaru směrování:

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

V příkladu použijte následující požadavky prohlížeče:

| Žádost                                    | Odpověď                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Ahoj, Martine!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Vyvolá výjimku s řetězcem "jejda!" |
| `http://localhost:5000/throw`              | Vyvolá výjimku s řetězcem "Uh oh!" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevine!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.

**Start (adresa URL\<řetězce, akce IRouteBuilder> routeBuilder)**

Použijte adresu URL a `IRouteBuilder`instanci :

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

Vytvoří stejný výsledek jako **Start(Action\<IRouteBuilder> routeBuilder)**, `http://localhost:8080`s výjimkou aplikace reaguje na .

**StartWith(Akce\<IApplicationBuilder> aplikace)**

Poskytněte delegátovi konfiguraci : `IApplicationBuilder`

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

Požádejte v prohlížeči `http://localhost:5000` o odpověď "Hello World!" `WaitForShutdown`bloky, dokud není vydána přestávka (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na ukončení stisknutí klávesy.

**StartWith (adresa url\<řetězce, akce IApplicationBuilder> aplikace)**

Zadejte adresu URL a `IApplicationBuilder`delegáta pro konfiguraci :

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

Vytvoří stejný výsledek jako **StartWith(Akce\<IApplicationBuilder> aplikace)**, `http://localhost:8080`s výjimkou aplikace reaguje na .

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>Rozhraní IWebHostEnvironment

Rozhraní `IWebHostEnvironment` poskytuje informace o webhostingovém prostředí aplikace. Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) `IWebHostEnvironment` k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
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

[Přístup založený na konvencích](xref:fundamentals/environments#environment-based-startup-class-and-methods) lze použít ke konfiguraci aplikace při spuštění na základě prostředí. Alternativně vstříkněte `IWebHostEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

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
> `IsDevelopment` Kromě rozšiřující metody `IWebHostEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody. Další informace naleznete v tématu <xref:fundamentals/environments>.

Služba `IWebHostEnvironment` může být také vložena `Configure` přímo do metody pro nastavení kanálu zpracování:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

`IWebHostEnvironment`lze vstříknout `Invoke` do metody při vytváření vlastního [middleware](xref:fundamentals/middleware/write):

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>Rozhraní IHostingEnvironment

[Rozhraní IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o webhostingovém prostředí aplikace. Použijte [vstřikování konstruktoru](xref:fundamentals/dependency-injection) `IHostingEnvironment` k získání, abyste mohli používat jeho vlastnosti a metody rozšíření:

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

[Přístup založený na konvencích](xref:fundamentals/environments#environment-based-startup-class-and-methods) lze použít ke konfiguraci aplikace při spuštění na základě prostředí. Alternativně vstříkněte `IHostingEnvironment` do konstruktoru `Startup` pro použití v `ConfigureServices`:

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
> `IsDevelopment` Kromě rozšiřující metody `IHostingEnvironment` nabízí `IsStaging`, `IsProduction`a `IsEnvironment(string environmentName)` metody. Další informace naleznete v tématu <xref:fundamentals/environments>.

Služba `IHostingEnvironment` může být také vložena `Configure` přímo do metody pro nastavení kanálu zpracování:

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

`IHostingEnvironment`lze vstříknout `Invoke` do metody při vytváření vlastního [middleware](xref:fundamentals/middleware/write):

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

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>Rozhraní IHostApplicationLifetime

`IHostApplicationLifetime`umožňuje činnosti po spuštění a vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.

| Token zrušení    | Aktivuje se při&#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | Hostitel byl plně spuštěn. |
| `ApplicationStopped`  | Hostitel dokončuje řádné vypnutí. Všechny požadavky by měly být zpracovány. Vypnutí blokuje, dokud tato událost nedokončí. |
| `ApplicationStopping` | Hostitel provádí řádné vypnutí. Požadavky mohou být stále zpracování. Vypnutí blokuje, dokud tato událost nedokončí. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
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

`StopApplication`požaduje ukončení aplikace. Následující třída `StopApplication` používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>Rozhraní IApplicationLifetime

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje aktivity po spuštění a vypnutí. Tři vlastnosti v rozhraní jsou tokeny zrušení používané k registraci `Action` metod, které definují události spuštění a vypnutí.

| Token zrušení    | Aktivuje se při&#8230; |
| --------------------- | --------------------- |
| [Aplikace spuštěna](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Hostitel byl plně spuštěn. |
| [Aplikace byla zastavena.](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Hostitel dokončuje řádné vypnutí. Všechny požadavky by měly být zpracovány. Vypnutí blokuje, dokud tato událost nedokončí. |
| [AplikaceZastavování](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Hostitel provádí řádné vypnutí. Požadavky mohou být stále zpracování. Vypnutí blokuje, dokud tato událost nedokončí. |

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

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) požaduje ukončení aplikace. Následující třída `StopApplication` používá k řádnému vypnutí aplikace při `Shutdown` volání metody třídy:

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

## <a name="scope-validation"></a>Ověření oboru

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` pokud je prostředí aplikace vývoj.

Je-li `ValidateScopes` `true`nastavena hodnota , provádí výchozí poskytovatel služeb kontroly, aby ověřil, že:

* Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.
* Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.

Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.

Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil. Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru. Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.

Chcete-li vždy ověřovat obory, včetně produkčního prostředí, nakonfigurujte [serviceprovideroptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) s [useDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrce hostitele:

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
