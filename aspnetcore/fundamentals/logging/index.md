---
title: Přihlášení do jádra rozhraní .NET core a ASP.NET core
author: rick-anderson
description: Zjistěte, jak používat architekturu protokolování poskytovanou balíčkem Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663301"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Přihlášení do jádra rozhraní .NET core a ASP.NET core

Tom [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)

Rozhraní .NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran. Tento článek ukazuje, jak používat rozhraní API protokolování s integrovanými zprostředkovateli.

::: moniker range=">= aspnetcore-3.0"

Většina příkladů kódu uvedených v tomto článku pochází z ASP.NET základních aplikací. Části těchto fragmentů kódu specifické pro protokolování platí pro všechny aplikace .NET Core, která používá [obecný hostitel](xref:fundamentals/host/generic-host). Příklad použití obecného hostitele v aplikaci, která není webovou konzolou, najdete<xref:fundamentals/host/hosted-services>v *Program.cs* soubor [ukázkové aplikace Úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).

Protokolování kódu pro aplikace bez obecného hostitele se liší ve způsobu, jakým [jsou přidány zprostředkovatele](#add-providers) a [jsou vytvořeny úhozy kláves](#create-logs). Příklady kódu bez hostitele jsou uvedeny v těchto částech článku.

::: moniker-end

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="add-providers"></a>Přidání zprostředkovatelů

Zprostředkovatel protokolování zobrazí nebo uloží protokoly. Například zprostředkovatel konzoly zobrazí protokoly na konzoli a poskytovatel Azure Application Insights je uloží do Azure Application Insights. Protokoly lze odeslat do více cílů přidáním více zprostředkovatelů.

::: moniker range=">= aspnetcore-3.0"

Pokud chcete přidat zprostředkovatele do aplikace, která používá `Add{provider name}` obecný hostitel, zavolejte metodu rozšíření poskytovatele v *Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

V aplikaci konzoly bez hostitele volejte `Add{provider name}` metodu rozšíření `LoggerFactory`zprostředkovatele při vytváření :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`a `AddConsole` vyžadují `using` prohlášení `Microsoft.Extensions.Logging`pro .

Výchozí ASP.NET volání výchozích <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu jádra , které přidá následující poskytovatele protokolování:

* [Konzola](#console-provider)
* [Ladit](#debug-provider)
* [Zdroj událostí](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (pouze při spuštění v systému Windows)

Výchozí zprostředkovatele můžete nahradit vlastními volbami. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Chcete-li přidat zprostředkovatele, `Add{provider name}` volejte metodu rozšíření zprostředkovatele v *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Předchozí kód vyžaduje odkazy `Microsoft.Extensions.Logging` na `Microsoft.Extensions.Configuration`a .

Výchozí šablona <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu volá , která přidá následující zprostředkovatele protokolování:

* Konzola
* Ladit
* EventSource (počínaje ASP.NET jádrem 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Pokud používáte `CreateDefaultBuilder`, můžete nahradit výchozí zprostředkovatele vlastními volbami. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte požadované zprostředkovatele.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Další informace o [předdefinovaných zprostředkovatelích protokolování](#built-in-logging-providers) a [poskytovatelích protokolování třetích stran](#third-party-logging-providers) dále v článku.

## <a name="create-logs"></a>Vytvořit protokoly

Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt. Ve webové aplikaci nebo hostované `ILogger` službě získáte z vkládání závislostí (DI). V aplikacích konzoly, `LoggerFactory` které nejsou `ILogger`hostitelskou konzolou, použijte k vytvoření aplikace .

Následující příklad ASP.NET Core vytvoří `TodoApiSample.Pages.AboutModel` protokolovací nástroj s jako kategorie. *Kategorie* protokolu je řetězec, který je přidružen ke každému protokolu. Instance `ILogger<T>` poskytované DI vytvoří protokoly, které mají plně `T` kvalifikovaný název typu jako kategorie. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Následující příklad aplikace konzoly bez hostitele `LoggingConsoleApp.Program` vytvoří protokolovací nástroj s jako kategorie.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

V následujících příkladech ASP.NET core a konzolové aplikace, `Information` protokolování se používá k vytvoření protokolů s jako úroveň. *Úroveň* protokolu označuje závažnost protokolované události. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Vytvoření protokolů ve třídě Program

Chcete-li zapsat `Program` protokoly ve třídě aplikace `ILogger` ASP.NET Core, získejte instanci z DI po vytvoření hostitele:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Protokolování během výstavby hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateHostBuilder`. `AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>Vytvoření protokolů ve třídě Startup

Chcete-li zapsat `Startup.Configure` protokoly v metodě `ILogger` aplikace ASP.NET Core, zahrňte parametr do podpisu metody:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Zápis protokolů před dokončením nastavení kontejneru DI v metodě `Startup.ConfigureServices` není podporován:

* Vkládání protokolů `Startup` do konstruktoru není podporováno.
* Vkládání protokolů `Startup.ConfigureServices` do podpisu metody není podporováno.

Důvodem pro toto omezení je, že protokolování závisí na DI a na konfiguraci, která zase závisí na DI. Kontejner DI není nastaven, `ConfigureServices` dokud nebude dokončena.

Konstruktor vkládání úhozů do `Startup` funguje v dřívějších verzích ASP.NET Core, protože samostatný kontejner DI je vytvořen pro web hostitele. Informace o tom, proč je pro obecný hostitel vytvořen pouze jeden kontejner, naleznete v [oznámení o změně rozdělení](https://github.com/aspnet/Announcements/issues/353).

Pokud potřebujete nakonfigurovat službu, `ILogger<T>`která závisí na , můžete to stále provést pomocí vkládání konstruktoru nebo poskytnutím metody výroby. Přístup metody výroby se doporučuje pouze v případě, že neexistuje žádná jiná možnost. Předpokládejme například, že potřebujete vyplnit vlastnost službou z DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Předchozí zvýrazněný kód `Func` je, který běží poprvé kontejner UD potřebuje `MyService`k vytvoření instance . Tímto způsobem můžete přistupovat k libovolné registrované službě.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Vytvoření protokolů při spuštění

Chcete-li zapsat `Startup` protokoly `ILogger` do třídy, zahrňte parametr do podpisu konstruktoru:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Vytvoření protokolů ve třídě Program

Chcete-li zapsat `Program` protokoly `ILogger` ve třídě, získejte instanci z DI:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Protokolování během výstavby hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se protokolovací nástroj [Serilog](https://serilog.net/) používá k přihlášení `CreateWebHostBuilder`. `AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Žádné asynchronní metody protokolování

Protokolování by mělo být tak rychlé, že nestojí za náklady na výkon asynchronního kódu. Pokud je úložiště dat protokolování pomalé, nezapisujte do něj přímo. Zvažte zápis zpráv protokolu do rychlého úložiště zpočátku a potom je přesunout do pomalé úložiště později. Například pokud se přihlašujete k serveru SQL Server, `Log` nechcete `Log` to provést přímo v metodě, protože metody jsou synchronní. Místo toho synchronně přidat zprávy protokolu do fronty v paměti a mají pracovník na pozadí vytáhnout zprávy z fronty provést asynchronní práci odesílání dat na SQL Server. Další informace naleznete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHub.

## <a name="configuration"></a>Konfigurace

Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:

* Formáty souborů (INI, JSON a XML).
* Argumenty příkazového řádku.
* Proměnné prostředí.
* Objekty .NET v paměti.
* Nešifrované úložiště [Správce tajných](xref:security/app-secrets) služeb.
* Šifrované úložiště uživatelů, například [Azure Key Vault](xref:security/key-vault-configuration).
* Vlastní zprostředkovatelé (nainstalované nebo vytvořené).

Například konfigurace protokolování je běžně `Logging` poskytována v části souborů nastavení aplikace. Následující příklad ukazuje obsah typické *nastavení aplikace. Development.json* soubor:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

Vlastnost `Logging` může `LogLevel` mít a protokolovat vlastnosti zprostředkovatele (je zobrazena konzola).

Vlastnost `LogLevel` v `Logging` části určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie. V příkladu `System` `Microsoft` a kategorie `Information` protokolu na úrovni `Debug` a všechny ostatní protokolu na úrovni.

Další vlastnosti v části `Logging` zadejte zprostředkovatele protokolování. Příklad je pro zprostředkovatele konzoly. Pokud zprostředkovatel podporuje [obory protokolu](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny. Vlastnost zprostředkovatele (například `Console` v příkladu) `LogLevel` může také určit vlastnost. `LogLevel`v rámci zprostředkovatele určuje úrovně pro protokolování pro tohoto zprostředkovatele.

Pokud jsou úrovně `Logging.{providername}.LogLevel`zadány v , `Logging.LogLevel`přepíší vše, co je nastaveno v .

Rozhraní API protokolování neobsahuje scénář pro změnu úrovní protokolu, když je spuštěná aplikace. Někteří poskytovatelé konfigurace jsou však schopni znovu načíst konfiguraci, která má okamžitý vliv na konfiguraci protokolování. Například [zprostředkovatel konfigurace souborů](xref:fundamentals/configuration/index#file-configuration-provider), který `CreateDefaultBuilder` je přidán do čtení souborů nastavení, znovu načte konfiguraci protokolování ve výchozím nastavení. Pokud se konfigurace změní v kódu, když je aplikace spuštěná, aplikace může volat [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) a aktualizovat konfiguraci protokolování aplikace.

Informace o implementaci zprostředkovatelů <xref:fundamentals/configuration/index>konfigurace naleznete v tématu .

## <a name="sample-logging-output"></a>Ukázkový výstup protokolování

S ukázkovým kódem zobrazeným v předchozí části se protokoly zobrazí v konzole při spuštění aplikace z příkazového řádku. Zde je příklad výstupu konzoly:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

Předchozí protokoly byly generovány provedením požadavku HTTP Get `http://localhost:5000/api/todo/0`do ukázkové aplikace na adrese .

Tady je příklad stejných protokolů, které se zobrazují v okně Ladění při spuštění ukázkové aplikace v sadě Visual Studio:

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApiSample". Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework. ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Protokoly, které jsou `ILogger` vytvořeny volání zobrazené v předchozí části začínají "TodoApi". Protokoly, které začínají kategorie "Microsoft" jsou z ASP.NET kódu core framework. ASP.NET Core a kód aplikace používají stejné protokolování ROZHRANÍ API a zprostředkovatelů.

::: moniker-end

Zbývající část tohoto článku vysvětluje některé podrobnosti a možnosti pro protokolování.

## <a name="nuget-packages"></a>Balíčky NuGet

Rozhraní `ILogger` `ILoggerFactory` a jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategorie protokolu

Při `ILogger` vytvoření objektu je pro něj *zadána kategorie.* Tato kategorie je součástí každé zprávy protokolu `ILogger`vytvořené danou instancí . Kategorie může být libovolný řetězec, ale konvence je použít název třídy, například "TodoApi.Controllers.TodoController".

Slouží `ILogger<T>` k `ILogger` získání instance, která používá `T` plně kvalifikovaný název typu jako kategorie:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Chcete-li kategorii explicitně `ILoggerFactory.CreateLogger`určit, volejte :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>`je ekvivalentní `CreateLogger` volání s plně kvalifikovaným typem názvu . `T`

## <a name="log-level"></a>Úroveň protokolu

Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu. Úroveň protokolu označuje závažnost nebo důležitost. Můžete například zapsat `Information` protokol, když metoda `Warning` končí normálně a protokol, když metoda vrátí stavový kód *404 Not Found.*

Následující kód `Information` vytvoří `Warning` a protokoly:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id). Druhým parametrem je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytnuté zbývajícími parametry metody. Parametry metody jsou vysvětleny v [části šablony zprávy](#log-message-template) dále v tomto článku.

Metody protokolu, které zahrnují úroveň v názvu `LogInformation` `LogWarning`metody (například a ) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Tyto metody `Log` volání metody, `LogLevel` která přebírá parametr. Můžete volat `Log` metodu přímo, nikoli jednu z těchto metod rozšíření, ale syntaxe je poměrně složité. Další informace naleznete <xref:Microsoft.Extensions.Logging.ILogger> v tématu a [rozšíření protokolů zdrojový kód](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definuje následující úrovně protokolu, seřazené zde od nejnižší k nejvyšší závažnosti.

* Trasování = 0

  Informace, které je obvykle cenné pouze pro ladění. Tyto zprávy mohou obsahovat citlivá data aplikace, a proto by neměly být povoleny v produkčním prostředí. *Ve výchozím nastavení zakázáno.*

* Ladění = 1

  Informace, které mohou být užitečné při vývoji a ladění. Příklad: `Entering method Configure with flag set to true.` `Debug` Povolit protokoly úrovně v produkčním prostředí pouze při řešení potíží, z důvodu velkého objemu protokolů.

* Informace = 2

  Pro sledování obecného toku aplikace. Tyto protokoly mají obvykle některé dlouhodobé hodnoty. Příklad: `Request received for path /api/todo`

* Upozornění = 3

  Pro neobvyklé nebo neočekávané události v toku aplikace. Mohou mezi ně patřit chyby nebo jiné podmínky, které nezpůsobují zastavení aplikace, ale může být nutné ji prozkoumat. Zpracované výjimky jsou běžné místo `Warning` pro použití na úrovni protokolu. Příklad: `FileNotFoundException for file quotes.txt.`

* Chyba = 4

  Pro chyby a výjimky, které nelze zpracovat. Tyto zprávy označují selhání v aktuální aktivity nebo operace (například aktuální požadavek HTTP), není selhání celé aplikace. Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`

* Kritické = 5

  Pro poruchy, které vyžadují okamžitou pozornost. Příklady: scénáře ztráty dat, nedostatek místa na disku.

Pomocí úrovně protokolu můžete určit, kolik výstupu protokolu je zapsáno do určitého paměťového média nebo okna zobrazení. Příklad:

* Ve výrobě:
  * Protokolování `Trace` na `Information` úrovních pro vytváří velký objem podrobných zpráv protokolu. Chcete-li řídit náklady a nepřekročit limity úložiště dat, zařazujte `Trace` zprávy `Information` na úrovni do úložiště dat s vysokým objemem a nízkými náklady.
  * Protokolování `Warning` `Critical` na úrovních obvykle vytváří méně, menší zprávy protokolu. Proto náklady a limity úložiště obvykle nejsou problém, což má za následek větší flexibilitu výběru úložiště dat.
* Během vývoje:
  * `Warning` Protokolovat `Critical` prostřednictvím zpráv do konzoly.
  * Při `Trace` `Information` potížích přidávejte zprávy.

Oddíl [Filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovatel zpracovává.

ASP.NET Core zapisuje protokoly pro rámcové události. Příklady protokolu dříve v tomto článku `Information` vyloučeny protokoly pod úrovní, takže byly vytvořeny žádné `Debug` protokoly nebo `Trace` úroveň. Tady je příklad protokolů konzoly vytvořených spuštěním `Debug` ukázkové aplikace nakonfigurované tak, aby zobrazovala protokoly:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>ID události protokolu

Každý protokol může určit *ID události*. Ukázková aplikace to provádí pomocí `LoggingEvents` místně definované třídy:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

ID události přidruží sadu událostí. Všechny protokoly související se zobrazením seznamu položek na stránce mohou být například 1001.

Zprostředkovatel protokolování může uložit ID události v poli ID, ve zprávě protokolování nebo vůbec. Poskytovatel ladění nezobrazuje ID událostí. Zprostředkovatel konzoly zobrazuje ID událostí v závorkách za kategorií:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Šablona protokolu zpráv

Každý protokol určuje šablonu zprávy. Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty. Používejte názvy pro zástupné symboly, ne čísla.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Pořadí zástupných symbolů, nikoli jejich názvy, určuje, které parametry se používají k zadání jejich hodnot. V následujícím kódu si všimněte, že názvy parametrů jsou mimo pořadí v šabloně zprávy:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:

```text
Parameter values: parm1, parm2
```

Architektura protokolování funguje tak, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Samotné argumenty jsou předány systému protokolování, nikoli pouze formátované šabloně zprávy. Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole. Předpokládejme například, že volání metody logger vypadají takto:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Pokud odesíláte protokoly do Azure Table Storage, každá `ID` `RequestTime` entita tabulky Azure může mít a vlastnosti, což zjednodušuje dotazy na data protokolu. Dotaz může najít všechny protokoly `RequestTime` v rámci určité oblasti bez analýzy doby mimo textovou zprávu.

## <a name="logging-exceptions"></a>Protokolování výjimek

Metody protokolování mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Různí poskytovatelé zpracovávají informace o výjimce různými způsoby. Tady je příklad výstupu zprostředkovatele ladění z výše uvedeného kódu.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrování protokolů

Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny zprostředkovatele nebo všechny kategorie. Všechny protokoly pod minimální úroveň nejsou předány tomuto zprostředkovateli, takže se nezobrazí ani neuloží.

Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu. Celá hodnota `LogLevel.None` je 6, která je `LogLevel.Critical` vyšší než (5).

### <a name="create-filter-rules-in-configuration"></a>Vytvoření pravidel filtru v konfiguraci

Kód šablony projektu `CreateDefaultBuilder` volá k nastavení protokolování pro zprostředkovatele konzoly, ladění a zdroje událostí (ASP.NET Core 2.2 nebo novějším). Metoda `CreateDefaultBuilder` nastaví protokolování hledat konfiguraci v `Logging` části, jak je vysvětleno dříve v tomto [článku](#configuration).

Konfigurační data určují minimální úrovně protokolu podle zprostředkovatele a kategorie, jako v následujícím příkladu:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Tento JSON vytvoří šest pravidel filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a jeden pro všechny zprostředkovatele. Jedno pravidlo je vybráno pro `ILogger` každého zprostředkovatele při vytvoření objektu.

### <a name="filter-rules-in-code"></a>Filtrování pravidel v kódu

Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Druhý `AddFilter` určuje zprostředkovatele ladění pomocí jeho název typu. První `AddFilter` platí pro všechny zprostředkovatele, protože neurčuje typ zprostředkovatele.

### <a name="how-filtering-rules-are-applied"></a>Jak se používají pravidla filtrování

Konfigurační `AddFilter` data a kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce. Prvních šest pocházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.

| Číslo | Poskytovatel      | Kategorie, které začínají ...          | Minimální úroveň protokolu |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Ladit         | Všechny kategorie                          | Informace       |
| 2      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Interní | Upozornění           |
| 3      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Ladit             |
| 4      | Konzola       | Microsoft.AspNetCore.Mvc.Razor          | Chyba             |
| 5      | Konzola       | Všechny kategorie                          | Informace       |
| 6      | Všichni poskytovatelé | Všechny kategorie                          | Ladit             |
| 7      | Všichni poskytovatelé | Systém                                  | Ladit             |
| 8      | Ladit         | Microsoft                               | Trasování             |

Při `ILogger` vytvoření objektu, `ILoggerFactory` objekt vybere jedno pravidlo pro zprostředkovatele použít na tento protokolování. Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel. Nejkonkrétnější pravidlo možné pro každého poskytovatele a dvojice kategorií je vybránz dostupných pravidel.

Následující algoritmus se používá pro `ILogger` každého zprostředkovatele při vytvoření pro danou kategorii:

* Vyberte všechna pravidla, která odpovídají poskytovateli nebo jeho aliasu. Pokud není nalezena žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.
* Z výsledku předchozího kroku vyberte pravidla s nejdelší předponou kategorie shody. Pokud není nalezena žádná shoda, vyberte všechna pravidla, která neurčují kategorii.
* Pokud je vybráno více pravidel, vezměte **poslední.**
* Pokud nejsou vybrána `MinimumLevel`žádná pravidla, použijte .

S předchozím seznamem pravidel předpokládejme, `ILogger` že vytvoříte objekt pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Pro zprostředkovatele ladění platí pravidla 1, 6 a 8. Pravidlo 8 je nejkonkrétnější, takže to je ten, který byl vybrán.
* Pro zprostředkovatele konzoly platí pravidla 3, 4, 5 a 6. Pravidlo 3 je nejkonkrétnější.

Výsledná `ILogger` instance odešle `Trace` protokoly úrovně a výše zprostředkovateli ladění. Protokoly `Debug` úrovně a výše jsou odesílány zprostředkovateli konzoly.

### <a name="provider-aliases"></a>Aliasy zprostředkovatele

Každý zprostředkovatel definuje *alias,* který lze použít v konfiguraci namísto plně kvalifikovaný název typu.  Pro předdefinované zprostředkovatele použijte následující aliasy:

* Konzola
* Ladit
* EventSource
* Eventlog
* TraceSource
* Soubor AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Výchozí minimální úroveň

Existuje nastavení minimální úrovně, které se projeví pouze v případě, že pro daného zprostředkovatele a kategorii neplatí žádná pravidla z konfigurace nebo kódu. Následující příklad ukazuje, jak nastavit minimální úroveň:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Pokud explicitně nenastavíte minimální úroveň, `Information`výchozí hodnota `Trace` je `Debug` , což znamená, že a protokoly jsou ignorovány.

### <a name="filter-functions"></a>Funkce filtrování

Funkce filtru je vyvolána pro všechny zprostředkovatele a kategorie, které nemají pravidla přiřazená podle konfigurace nebo kódu. Kód ve funkci má přístup k typu zprostředkovatele, kategorii a úrovni protokolu. Příklad:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Systémové kategorie a úrovně

Zde jsou některé kategorie používané ASP.NET jádrem a jádrem entity s poznámkami o tom, jaké protokoly od nich lze očekávat:

| Kategorie                            | Poznámky |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Obecná diagnostika ASP.NET jádra. |
| Ochrana Microsoft.AspNetCore.DataProtection | Které klíče byly zváženy, nalezeny a použity. |
| Filtrování Microsoft.AspNetCore.HostFiltering  | Hostitelé povoleni. |
| Microsoft.AspNetCore.Hosting        | Jak dlouho trvalo dokončení požadavků HTTP a v jakém čase byly spuštěny. Která hostitelská spouštěcí sestavení byla načtena. |
| Microsoft.AspNetCore.Mvc            | Diagnostika MVC a Razor. Vazba modelu, spuštění filtru, kompilace zobrazení, výběr akce. |
| Microsoft.AspNetCore.Routing        | Informace o porovnávání tras. |
| Microsoft.AspNetCore.Server         | Připojení spustit, zastavit a udržet naživu odpovědi. informace o certifikátu HTTPS. |
| Soubory Microsoft.AspNetCore.StaticFiles    | Doručené složky. |
| Microsoft.EntityFrameworkCore       | Diagnostika jádra obecnéentity frameworku. Databázová aktivita a konfigurace, detekce změn, migrace. |

## <a name="log-scopes"></a>Obory protokolu

 *Obor* může seskupit sadu logických operací. Toto seskupení lze použít k připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady. Například každý protokol vytvořený jako součást zpracování transakce může obsahovat ID transakce.

Obor je `IDisposable` typ, který je <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> vrácen metodou a trvá, dokud je uvolněn. Použití oboru zabalením volání `using` protokolování v bloku:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Následující kód umožňuje obory pro zprostředkovatele konzoly:

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> Konfigurace možnosti `IncludeScopes` protokolování konzoly je nutná k povolení protokolování založeného na oboru.
>
> Informace o konfiguraci naleznete v části [Konfigurace.](#configuration)

Každá zpráva protokolu obsahuje informace s vymezenou oborem:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Vestavění poskytovatelé protokolování

ASP.NET Core dodává tyto poskytovatele:

* [Konzola](#console-provider)
* [Ladit](#debug-provider)
* [Zdroj událostí](#event-source-provider)
* [Eventlog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [Soubor AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Informace o protokolování stdout a ladění pomocí <xref:test/troubleshoot-azure-iis> ASP.NET <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>core modulem naleznete v tématu a .

### <a name="console-provider"></a>Zprostředkovatel konzoly

Balíček zprostředkovatele [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly. 

```csharp
logging.AddConsole();
```

Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Zprostředkovatel ladění

Balíček zprostředkovatele [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí`Debug.WriteLine` [třídy System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (volání metody).

V systému Linux tento zprostředkovatel zapisuje protokoly do */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Zprostředkovatel zdroje událostí

Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na `Microsoft-Extensions-Logging`křížovou platformu Zdroje událostí s názvem . V systému Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Zprostředkovatel zdroje událostí je `CreateDefaultBuilder` přidán automaticky, když je volána k sestavení hostitele.

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>nástroje pro trasování dotnet

[Dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) nástroj je globální nástroj rozhraní se sídlem v rychléplatformě, který umožňuje shromažďování trasování .NET Core spuštěného procesu. Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>pomocí .

Nainstalujte nástroje trasování dotnet pomocí následujícího příkazu:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Pomocí nástrojů sledování dotnet shromažďujte trasování z aplikace:

1. Pokud aplikace nevytváří hostitele s `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.

1. Spusťte aplikaci pomocí příkazu. `dotnet run`

1. Určete identifikátor procesu (PID) aplikace .NET Core:

   * V systému Windows použijte jeden z následujících přístupů:
     * Správce úloh (Ctrl+Alt+Del)
     * [seznam úloh, příkaz](/windows-server/administration/windows-commands/tasklist)
     * [Příkaz Získat proces powershellu](/powershell/module/microsoft.powershell.management/get-process)
   * Na Linuxu použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Najděte PID pro proces, který má stejný název jako sestavení aplikace.

1. Spusťte `dotnet trace` příkaz.

   Obecná syntaxe příkazu:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Při použití příkazového prostředí Prostředí `--providers` PowerShell uzavřete hodnotu do jednoduchých uvozovek (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformách jiných než `-f speedscope` Windows přidejte možnost změnit formát `speedscope`výstupního trasovacího souboru na .

   | Klíčové slovo | Popis |
   | :-----: | ----------- |
   | 1       | Protokolovat meta `LoggingEventSource`události o . Nezaznamenává události z `ILogger`). |
   | 2       | Zapne událost, `Message` `ILogger.Log()` když je volána. Poskytuje informace programovým (neformátovaným) způsobem. |
   | 4       | Zapne událost, `FormatMessage` `ILogger.Log()` když je volána. Poskytuje formátovanou verzi řetězce informací. |
   | 8       | Zapne událost, `MessageJson` `ILogger.Log()` když je volána. Poskytuje json reprezentaci argumentů. |

   | Úroveň události | Popis     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`položky `{Logger Category}` `{Event Level}` pro a představují další podmínky filtrování protokolů. Samostatné `FilterSpecs` položky s`;`středníkem ( ).

   Příklad použití příkazového prostředí systému `--providers` Windows **(žádné** jednoduché uvozovky kolem hodnoty):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Předchozí příkaz aktivuje:

   * Protokolovací nástroj Zdroje událostí pro`4`vytvoření formátovaných`2`řetězců ( ) pro chyby ( ).
   * `Microsoft.AspNetCore.Hosting`protokolování `Informational` na úrovni`4`protokolování ( ).

1. Zastavení nástroje trasování dotnet stisknutím klávesy Enter nebo Ctrl+C.

   Trasování je uložens s názvem *trace.nettrace* ve složce, `dotnet trace` kde je příkaz proveden.

1. Otevřete trasování pomocí [perfview](#perfview). Otevřete soubor *trace.nettrace* a prozkoumejte události trasování.

Další informace naleznete v tématu:

* [Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace jádra.NET)
* [Trasování pro nástroj pro analýzu výkonu (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace úložiště dotnet/diagnostics GitHub)
* [Třída LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní API.NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Název reference LoggingEventSource (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Chcete-li získat referenční zdroj `release/{Version}`pro `{Version}` jinou verzi, změňte větev na , kde je požadovaná verze ASP.NET Core.
* [Perfview](#perfview) &ndash; Užitečné pro zobrazení trasování zdroje událostí.

#### <a name="perfview"></a>Zobrazení Perfview

::: moniker-end

Pomocí [nástroje PerfView](https://github.com/Microsoft/perfview) sbírat a zobrazovat protokoly. Existují další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi ETW vyzařované ASP.NET Core.

Chcete-li nakonfigurovat PerfView pro shromažďování událostí `*Microsoft-Extensions-Logging` protokolovaných tímto zprostředkovatelem, přidejte řetězec do seznamu **Další zprostředkovatelé.** (Nenechte si ujít hvězdičku na začátku řetězce.)

![Perfview Další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Zprostředkovatel služby Windows EventLog

Balíček zprostředkovatele [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.

```csharp
logging.AddEventLog();
```

[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Pokud `null` není zadáno, použije se následující výchozí nastavení:

* `LogName`&ndash; "Aplikace"
* `SourceName`&ndash; ".NET Runtime"
* `MachineName`&ndash; místní počítač

Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level). Chcete-li protokolovat události nižší než `Warning`, explicitně nastavte úroveň protokolu. Do souboru *appsettings.json* například přidejte následující:

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Zprostředkovatel TraceSource

Balíček [zprostředkovatele Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a zprostředkovatele.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource přetížení](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.

Chcete-li použít tohoto zprostředkovatele, aplikace musí běžet na rozhraní .NET Framework (spíše než .NET Core). Zprostředkovatel může směrovat zprávy do různých [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použité v ukázkové aplikaci.

### <a name="azure-app-service-provider"></a>Poskytovatel služeb Azure App Service

Balíček zprostředkovatele [Microsoft.Extensions.Log.Log.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Služby Azure App Service a do [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Balíček zprostředkovatele není součástí sdíleného rozhraní. Chcete-li použít zprostředkovatele, přidejte balíček zprostředkovatele do projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Balíček zprostředkovatele není součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Při cílení na rozhraní .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` metabalíček přidejte balíček zprostředkovatele do projektu. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Chcete-li konfigurovat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> nastavení <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>zprostředkovatele, použijte a , jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje projít v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Objekt nastavení může přepsat výchozí nastavení, například šablonu výstupu protokolování, název objektu blob a omezení velikosti souboru. (Výstupní*šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním `ILogger` metody.)

::: moniker-end

Když se nasadíte do aplikace App Service, aplikace respektuje nastavení v části [protokoly služby App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stránce **Služby aplikací** na portálu Azure. Při aktualizaci následujících nastavení se změny projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.

* **Protokolování aplikací (souborový systém)**
* **Protokolování aplikací (objekt blob)**

Výchozí umístění souborů protokolu je ve složce *D:\\\\home LogFiles\\Application* a výchozí název souboru je *diagnostics-yyyymmdd.txt*. Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2. Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure. Nemá žádný vliv, když je&mdash;projekt spuštěn místně, nezapisuje do místních souborů nebo místní vývojové úložiště pro objekty BLOB.

#### <a name="azure-log-streaming"></a>Streamování protokolu Azure

Streamování protokolu Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:

* Aplikační server
* Webový server
* Trasování neúspěšných požadavků

Konfigurace streamování protokolu Azure:

* Přejděte na stránku **protokolů služby App Service** na stránce portálu aplikace.
* Nastavení **protokolování aplikací (souborový systém)** na **zapnuto**.
* Zvolte **úroveň**protokolu . Toto nastavení platí jenom pro streamování protokolu Azure, ne pro jiné poskytovatele protokolování v aplikaci.

Přejděte na stránku **Log Stream** a zobrazte zprávy aplikací. Jsou zaznamenány aplikací prostřednictvím `ILogger` rozhraní.

### <a name="azure-application-insights-trace-logging"></a>Protokolování trasování Azure Application Insights

Balíček zprostředkovatele [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights. Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu telemetrických dat. Pokud používáte tohoto zprostředkovatele, můžete dotazovat a analyzovat protokoly pomocí nástrojů Application Insights.

Zprostředkovatel protokolování je součástí jako závislost [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje všechny dostupné telemetrie pro ASP.NET Core. Pokud používáte tento balíček, není nutné instalovat balíček zprostředkovatele.

Nepoužívejte balíček&mdash; [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) pro ASP.NET 4.x.

Další informace najdete v následujících materiálech:

* [Přehled služby Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pro ASP.NET základní aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou škálu telemetrie Application Insights spolu s protokolováním.
* [ApplicationInsightsLoggerProvider pro protokoly ILogger jádra .NET](/azure/azure-monitor/app/ilogger) – začněte zde, pokud chcete implementovat poskytovatele protokolování bez zbytku telemetrie Application Insights.
* [Adaptéry pro protokolování Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Nainstalujte, nakonfigurujte a inicializujte kurz Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.

## <a name="third-party-logging-providers"></a>Poskytovatelé protokolování třetích stran

Architektury protokolování třetích stran, které pracují s ASP.NET Core:

* [elmah.io](https://elmah.io/) ([Úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([Úložiště GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([Úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště Github](https://github.com/googleapis/google-cloud-dotnet))

Některé architektury jiných výrobců můžete provádět [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Použití rozhraní třetí strany je podobné použití jednoho z předdefinovaných zprostředkovatelů:

1. Přidejte balíček NuGet do projektu.
1. Volání `ILoggerFactory` metody rozšíření poskytované rozhraní protokolování.

Další informace naleznete v dokumentaci každého zprostředkovatele. Společnost Microsoft nepodporuje poskytovatele protokolování jiných výrobců.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/loggermessage>
