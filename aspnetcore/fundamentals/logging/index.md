---
title: Přihlášení v .NET Core a ASP.NET Core
author: rick-anderson
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 23ce2d09d2ce9f415ce71bcd7c21c29cb2a040fc
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550364"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Přihlášení v .NET Core a ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)

.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.

::: moniker range=">= aspnetcore-3.0"

Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích. Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host). Informace o tom, jak používat obecného hostitele v aplikacích nevyužívajících webovou konzolu, najdete v tématu [hostované služby](xref:fundamentals/host/hosted-services).

Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů. Příklady kódu, který není hostitelský, jsou uvedeny v těchto částech článku.

::: moniker-end

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Přidat zprostředkovatele

Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly. Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights. Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.

::: moniker range=">= aspnetcore-3.0"

Chcete-li přidat poskytovatele v aplikaci, která používá obecného hostitele, zavolejte metodu rozšíření `Add{provider name}` poskytovatele v *program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

V konzolové aplikaci, která není hostitelem, zavolejte při vytváření `LoggerFactory`metodu rozšíření `Add{provider name}` poskytovatele:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` a `AddConsole` vyžadují příkaz `using` pro `Microsoft.Extensions.Logging`.

Výchozí ASP.NET Core šablony projektů volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, které přidávají následující zprostředkovatele protokolování:

* Konzola
* Ladit
* EventSource
* Protokol událostí (pouze při spuštění v systému Windows)

Výchozí poskytovatele můžete nahradit vašimi vlastními možnostmi. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Chcete-li přidat poskytovatele, zavolejte metodu rozšíření `Add{provider name}` poskytovatele v *program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a `Microsoft.Extensions.Configuration`.

Výchozí šablona projektu volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, která přidává následující zprostředkovatele protokolování:

* Konzola
* Ladit
* EventSource (počínaje ASP.NET Core 2,2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Pokud používáte `CreateDefaultBuilder`, můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.

## <a name="create-logs"></a>Vytvořit protokoly

K vytváření protokolů použijte objekt <xref:Microsoft.Extensions.Logging.ILogger%601>. Ve webové aplikaci nebo v hostované službě získáte `ILogger` od injektáže závislosti (DI). V nehostitelských konzolových aplikacích použijte k vytvoření `ILogger``LoggerFactory`.

Následující ASP.NET Core příklad vytvoří protokolovací nástroj s `TodoApiSample.Pages.AboutModel` jako kategorie. *Kategorie* protokolu je řetězec, který je spojený s každým protokolem. Instance `ILogger<T>` poskytovaná pomocí DI vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Následující příklad nehostitelských konzolových aplikací vytvoří protokolovací nástroj s `LoggingConsoleApp.Program` jako kategorie.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s `Information` jako úroveň používá protokolovací nástroj. *Úroveň* protokolu označuje závažnost protokolované události. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Vytvořit protokoly ve třídě program

Chcete-li zapisovat protokoly ve třídě `Program` aplikace ASP.NET Core, po sestavení hostitele získat instanci `ILogger` od typu DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Protokolování během konstrukce hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se k přihlášení `CreateHostBuilder`používá protokolovací nástroj [Serilog](https://serilog.net/) . `AddSerilog` používá statickou konfiguraci určenou v `Log.Logger`:

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

### <a name="create-logs-in-the-startup-class"></a>Vytvořit protokoly ve spouštěcí třídě

Pokud chcete zapisovat protokoly v metodě `Startup.Configure` ASP.NET Core aplikace, zahrňte do signatury metody parametr `ILogger`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Zápis protokolů před dokončením nastavení typu DI Container v metodě `Startup.ConfigureServices` není podporován:

* Vkládání protokolovacího nástroje do konstruktoru `Startup` není podporováno.
* Vkládání protokolovacího nástroje do signatury metody `Startup.ConfigureServices` se nepodporuje.

Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI. Kontejner DI není nastavený, dokud `ConfigureServices` nedokončené.

Vložení konstruktoru protokolovacího nástroje do `Startup` funguje v dřívějších verzích ASP.NET Core, protože pro webového hostitele se vytvoří samostatný kontejner DI. Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).

Pokud potřebujete nakonfigurovat službu, která závisí na `ILogger<T>`, můžete to provést pomocí injektáže konstruktoru nebo poskytnutím metody továrny. Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost. Předpokládejme například, že potřebujete vyplnit vlastnost pomocí služby z DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Předchozí zvýrazněný kód je `Func`, který se spustí, když DI Container potřebuje vytvořit instanci `MyService`. Tímto způsobem můžete získat přístup k libovolné registrované službě.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Vytvořit protokoly při spuštění

Chcete-li zapisovat protokoly ve třídě `Startup`, zahrňte do signatury konstruktoru `ILogger` parametr:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Vytvořit protokoly ve třídě program

Chcete-li zapisovat protokoly ve třídě `Program`, Získejte instanci `ILogger` od DI:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Protokolování během konstrukce hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se k přihlášení `CreateWebHostBuilder`používá protokolovací nástroj [Serilog](https://serilog.net/) . `AddSerilog` používá statickou konfiguraci určenou v `Log.Logger`:

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

### <a name="no-asynchronous-logger-methods"></a>Žádné metody asynchronního protokolovacího nástroje

Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu. Pokud je úložiště dat protokolování pomalé, nezapište ho přímo. Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště. Například pokud se přihlašujete k SQL Server, nechcete to provést přímo v metodě `Log`, protože `Log` metody jsou synchronní. Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server. Další informace najdete v [tomto](https://github.com/aspnet/AspNetCore.Docs/issues/11801) problému GitHubu.

## <a name="configuration"></a>Konfigurace

Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:

* Formáty souborů (INI, JSON a XML).
* Argumenty příkazového řádku.
* Proměnné prostředí.
* Objekty rozhraní .NET v paměti.
* Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .
* Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno).

Například konfigurace protokolování je běžně poskytována v části `Logging` souborů nastavení aplikace. Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :

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

Vlastnost `Logging` může mít `LogLevel` a vlastnosti poskytovatele protokolu (zobrazí se konzola).

Vlastnost `LogLevel` v rámci `Logging` určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie. V příkladu se kategorie `System` a `Microsoft` protokolují na `Information` úrovni a všechny ostatní se přihlašují na `Debug` úrovni.

Další vlastnosti v části `Logging` zadejte poskytovatele protokolování. Příklad je pro poskytovatele konzoly. Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` určuje, zda jsou povoleny. Vlastnost poskytovatele (například `Console` v příkladu) může také určovat vlastnost `LogLevel`. `LogLevel` pod poskytovatelem Určuje úrovně, které se mají protokolovat pro daného poskytovatele.

Pokud jsou v `Logging.{providername}.LogLevel`úrovně zadané, přepisují cokoli nastavené v `Logging.LogLevel`.

Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná. Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování. Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider), který je přidán pomocí `CreateDefaultBuilder` ke čtení souborů nastavení, znovu načte konfiguraci protokolování do výchozího nastavení. Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.

Informace o implementaci zprostředkovatelů konfigurace najdete v tématu <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku. Tady je příklad výstupu konzoly:

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

Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na `http://localhost:5000/api/todo/0`.

Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:

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

Protokoly, které jsou vytvořeny pomocí `ILogger` volání uvedená v předchozí části, začínají na "TodoApiSample". Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

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

Protokoly, které jsou vytvořeny pomocí `ILogger` volání uvedená v předchozí části, začínají na "TodoApi". Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

::: moniker-end

Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.

## <a name="nuget-packages"></a>Balíčky NuGet

Rozhraní `ILogger` a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. abstrakce](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategorie protokolu

Je-li vytvořen objekt `ILogger`, je pro něj zadána *kategorie* . Tato kategorie je součástí každé zprávy protokolu vytvořené touto instancí `ILogger`. Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".

Pomocí `ILogger<T>` získat `ILogger` instanci, která jako kategorii používá plně kvalifikovaný název typu `T`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T`.

## <a name="log-level"></a>Úroveň protokolu

Každý protokol určuje <xref:Microsoft.Extensions.Logging.LogLevel>ou hodnotu. Úroveň protokolu označuje závažnost nebo důležitost. Můžete například zapsat protokol `Information`, když metoda končí normálně, a protokol `Warning`, když metoda vrátí stavový kód 404, který *nebyl nalezen* .

Následující kód vytvoří protokoly `Information` a `Warning`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id). Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody. Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.

Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Tyto metody volají metodu `Log`, která přebírá `LogLevel` parametr. Metodu `Log` můžete volat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá. Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.

ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.

* Trasování = 0

  Informace, které jsou obvykle užitečné pouze pro ladění. Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí. *Ve výchozím nastavení zakázáno.*

* Ladění = 1

  Informace, které mohou být užitečné při vývoji a ladění. Příklad: `Entering method Configure with flag set to true.` povolit protokoly `Debug` úrovně v produkčním prostředí jenom při odstraňování potíží kvůli velkému objemu protokolů.

* Informace = 2

  Pro sledování obecného toku aplikace. Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu. Příklad: `Request received for path /api/todo`

* Upozornění = 3

  Pro neobvyklé nebo neočekávané události v toku aplikace. Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat. Zpracování výjimek je běžné místo pro použití úrovně protokolu `Warning`. Příklad: `FileNotFoundException for file quotes.txt.`

* Chyba = 4

  Pro chyby a výjimky, které nelze zpracovat. Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace. Příklad zprávy protokolu: `Cannot insert record due to duplicate key violation.`

* Kritické = 5

  Chyby, které vyžadují okamžitou pozornost. Příklady: scénáře ztráty dat, nedostatek místa na disku.

Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení. Příklad:

* V produkčním prostředí:
  * Protokolování na `Trace` přes `Information` úrovně vytváří velké množství podrobných zpráv protokolu. Pokud chcete řídit náklady a nepřekračuje limity úložiště dat, protokolujte `Trace` prostřednictvím zpráv na úrovni `Information` na vysoce velké úložiště dat s nízkými náklady na objem.
  * Protokolování na `Warning` přes `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu. Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.
* Během vývoje:
  * Protokoluje `Warning` prostřednictvím `Critical` zpráv do konzoly.
  * Při řešení potíží přidejte `Trace` do `Information` zpráv.

Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.

ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework. Příklady protokolů výše v tomto článku vyloučily protokoly pod úrovní `Information`, takže se nevytvořily žádné `Debug` ani protokoly úrovní `Trace`. Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované tak, aby zobrazovaly `Debug` protokoly:

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

Každý protokol může určovat *ID události*. Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

ID události přidružuje sadu událostí. Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.

Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne. Zprostředkovatel ladění nezobrazuje ID událostí. Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Šablona zprávy protokolu

Každý protokol Určuje šablonu zprávy. Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty. Použijte názvy zástupných symbolů, nikoli čísla.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot. V následujícím kódu si všimněte, že názvy parametrů jsou v šabloně zprávy mimo pořadí:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Tento kód vytvoří zprávu protokolu s hodnotami parametrů v posloupnosti:

```text
Parameter values: parm1, parm2
```

Protokolovací rozhraní funguje tímto způsobem, aby zprostředkovatelé protokolování mohli implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy. Tyto informace umožňují poskytovatelům protokolování ukládat hodnoty parametrů jako pole. Předpokládejme například, že volání metody protokolovacího nástroje vypadá takto:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Pokud odesíláte protokoly do Azure Table Storage, Každá entita tabulky Azure může mít vlastnosti `ID` a `RequestTime`, což zjednodušuje dotazy na data protokolu. Dotaz může najít všechny protokoly v rámci určitého rozsahu `RequestTime`, aniž by bylo potřeba analyzovat časový limit textové zprávy.

## <a name="logging-exceptions"></a>Protokolování výjimek

Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Různí poskytovatelé zpracovávají informace o výjimce různými způsoby. Tady je příklad výstupu poskytovatele ladění z kódu uvedeného výše.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrování protokolů

Můžete zadat minimální úroveň protokolu pro konkrétního zprostředkovatele a kategorii nebo pro všechny poskytovatele nebo všechny kategorie. Všechny protokoly nižší než minimální úroveň nebudou tomuto poskytovateli předány, takže se nebudou zobrazovat ani ukládat.

Chcete-li potlačit všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolu. Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Vytvořit pravidla filtru v konfiguraci

Kód šablony projektu volá `CreateDefaultBuilder` pro nastavení protokolování pro poskytovatele konzoly, ladění a EventSource (ASP.NET Core 2,2 nebo novější). Metoda `CreateDefaultBuilder` nastaví protokolování tak, aby hledalo konfiguraci v `Logging` oddílu, jak je vysvětleno [výše v tomto článku](#configuration).

Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele. Když se vytvoří objekt `ILogger`, vybere se pro každého poskytovatele jedno pravidlo.

### <a name="filter-rules-in-code"></a>Filtrovat pravidla v kódu

Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu. První `AddFilter` platí pro všechny poskytovatele, protože nespecifikuje typ poskytovatele.

### <a name="how-filtering-rules-are-applied"></a>Jak se používají pravidla filtrování

Konfigurační data a kód `AddFilter` zobrazené v předchozích příkladech vytvoří pravidla uvedená v následující tabulce. Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.

| Počet | Zprostředkovatel      | Kategorie, které začínají na...          | Minimální úroveň protokolování |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Ladit         | Všechny kategorie                          | Informace o nástroji       |
| 2      | Konzola       | Microsoft. AspNetCore. Mvc. Razor. Internal | Upozornění           |
| 3      | Konzola       | Microsoft. AspNetCore. Mvc. Razor. Razor    | Ladit             |
| 4      | Konzola       | Microsoft. AspNetCore. Mvc. Razor          | Chyba             |
| 5      | Konzola       | Všechny kategorie                          | Informace o nástroji       |
| 6      | Všichni poskytovatelé | Všechny kategorie                          | Ladit             |
| 7      | Všichni poskytovatelé | Systém                                  | Ladit             |
| 8      | Ladit         | Síťový                               | Trasování             |

Když se vytvoří objekt `ILogger`, vybere objekt `ILoggerFactory` jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj. Všechny zprávy napsané instancí `ILogger` jsou filtrovány na základě vybraných pravidel. V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.

Následující algoritmus se používá pro každého poskytovatele, když se pro danou kategorii vytvoří `ILogger`:

* Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem. Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.
* Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie. Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.
* Pokud je vybráno více pravidel, vezměte **Poslední** z nich.
* Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel`.

Pomocí předchozího seznamu pravidel Předpokládejme, že vytvoříte objekt `ILogger` pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":

* Pro poskytovatele ladění platí pravidla 1, 6 a 8. Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.
* Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6. Pravidlo 3 je nejvíce specifické.

Výsledná instance `ILogger` odesílá protokoly úrovně `Trace` a výše do poskytovatele ladění. Do poskytovatele konzoly se odešlou protokoly `Debug` úrovně a vyšší.

### <a name="provider-aliases"></a>Aliasy zprostředkovatele

Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.  Pro předdefinované poskytovatele použijte následující aliasy:

* Konzola
* Ladit
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Výchozí minimální úroveň

Existuje nastavení minimální úrovně, které se projeví pouze v případě, že se pro daného zprostředkovatele a kategorii nepoužijí žádná pravidla z konfigurace nebo kódu. Následující příklad ukazuje, jak nastavit minimální úroveň:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že protokoly `Trace` a `Debug` se ignorují.

### <a name="filter-functions"></a>Funkce filtru

Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu. Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu. Příklad:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Systémové kategorie a úrovně

Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:

| Kategorie                            | Poznámky |
| ----------------------------------- | ----- |
| Microsoft. AspNetCore                | Obecná diagnostika ASP.NET Core. |
| Microsoft. AspNetCore. DataProtection | Které klíče byly zváženy, nalezeny a použity. |
| Microsoft. AspNetCore. HostFiltering  | Hostitelé povoleni. |
| Microsoft. AspNetCore. hosting        | Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění. Která hostující spouštěcí sestavení byla načtena. |
| Microsoft. AspNetCore. Mvc            | Diagnostika MVC a Razor Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce. |
| Microsoft. AspNetCore. Routing        | Informace o shodě trasy. |
| Microsoft. AspNetCore. Server         | Připojení – spouštění, zastavování a udržování reakcí na Alive. Informace o certifikátu HTTPS |
| Microsoft. AspNetCore. StaticFiles    | Soubory byly obsluhovány. |
| Microsoft. EntityFrameworkCore       | Obecná diagnostika Entity Framework Core. Databázová aktivita a konfigurace, detekce změn, migrace. |

## <a name="log-scopes"></a>Rozsahy protokolů

 *Obor* může seskupit sadu logických operací. Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady. Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.

Obor je typ `IDisposable`, který je vrácen metodou <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> a trvá, dokud nebude uvolněn. Použijte rozsah vybalením volání protokolovacího nástroje v `using`ovém bloku:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Následující kód umožňuje obory pro poskytovatele konzoly:

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> Pro povolení protokolování založeného na rozsahu je nutná konfigurace možnosti protokolovacího nástroje konzoly `IncludeScopes`.
>
> Informace o konfiguraci najdete v části věnované [konfiguraci](#configuration) .

Každá zpráva protokolu obsahuje informace s vymezeným oborem:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Vestavění zprostředkovatelé protokolování

ASP.NET Core dodává následující poskytovatele:

* [Console](#console-provider)
* [Ladí](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Poskytovatel konzoly

Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly. 

```csharp
logging.AddConsole();
```

Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Poskytovatel ladění

Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` volání metody).

V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Zprostředkovatel zdroje událostí

Balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging`. Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Poskytovatel zdroje událostí se přidá automaticky, když se zavolá `CreateDefaultBuilder` k sestavení hostitele.

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>nástroje pro trasování dotnet

Nástroj [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) je globální nástroj CLI pro různé platformy, který umožňuje shromažďování trasování .NET Core pro běžící proces. Nástroj shromažďuje data poskytovatele <xref:Microsoft.Extensions.Logging.EventSource> pomocí <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Nainstalujte nástroje pro trasování dotnet pomocí následujícího příkazu:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Shromažďování trasování z aplikace pomocí trasovacích nástrojů dotnet:

1. Pokud aplikace nevytvoří hostitele s `CreateDefaultBuilder`, přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.

1. Spusťte aplikaci pomocí příkazu `dotnet run`.

1. Určení identifikátoru procesu (PID) aplikace .NET Core:

   * Ve Windows použijte některý z následujících přístupů:
     * Správce úloh (Ctrl + Alt + Del)
     * [tasklist – příkaz](/windows-server/administration/windows-commands/tasklist)
     * [Příkaz PowerShellu Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * V systému Linux použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Vyhledejte kód PID pro proces, který má stejný název jako sestavení aplikace.

1. Spusťte příkaz `dotnet trace`.

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

   Při použití příkazového prostředí PowerShellu uzavřete `--providers`ovou hodnotu do jednoduchých uvozovek (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformách jiných než Windows přidejte možnost `-f speedscope`, abyste změnili formát výstupního souboru trasování na `speedscope`.

   | Klíčové slovo | Popis |
   | :-----: | ----------- |
   | 1       | Protokoluje meta události týkající se `LoggingEventSource`. Neprotokoluje události z `ILogger`). |
   | 2       | Zapíná událost `Message` při volání `ILogger.Log()`. Poskytuje informace v programovém (neformátovaném) způsobu. |
   | 4       | Zapíná událost `FormatMessage` při volání `ILogger.Log()`. Poskytuje formátovanou verzi řetězce informací. |
   | 8       | Zapíná událost `MessageJson` při volání `ILogger.Log()`. Poskytuje reprezentace argumentů ve formátu JSON. |

   | Úroveň události | Popis     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   položky `FilterSpecs` pro `{Logger Category}` a `{Event Level}` reprezentují další podmínky filtrování protokolů. Oddělte položky `FilterSpecs` středníkem (`;`).

   Příklad použití příkazového prostředí systému Windows (**bez** jednoduchých uvozovek kolem hodnoty `--providers`):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Předchozí příkaz se aktivuje:

   * Protokolovací nástroj zdroje událostí pro vytváření formátovaných řetězců (`4`) pro chyby (`2`).
   * `Microsoft.AspNetCore.Hosting` protokolování na úrovni protokolování `Informational` (`4`).

1. Kliknutím na klávesu ENTER nebo stisknutím kombinace kláves CTRL + C zastavte nástroje pro trasování dotnet.

   Trasování je uloženo s názvem *Trace. nettrace* ve složce, ve které je spuštěný příkaz `dotnet trace`.

1. Otevřete trasování pomocí [PerfView](#perfview). Otevřete soubor *Trace. nettrace* a prozkoumejte události trasování.

Další informace najdete v části .

* [Trasování pro nástroj Analýza výkonu (dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace k .NET Core)
* [Trasování pro nástroj Analýza výkonu (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace k úložišti GitHub/Diagnostika)
* [LoggingEventSource – třída](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní .NET API)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource reference Source (3,0 &ndash;)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) Chcete-li získat zdroj odkazů pro jinou verzi, změňte větev na `release/{Version}`, kde `{Version}` je verze ASP.NET Core požadovaná.
* [Perfview](#perfview) &ndash; užitečné pro zobrazení trasování zdroje událostí.

#### <a name="perfview"></a>PerfView

::: moniker-end

K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) . Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.

Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **dalších zprostředkovatelů** . (Na začátku řetězce nechybíš hvězdičku.)

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Zprostředkovatel událostí systému Windows

Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.

```csharp
logging.AddEventLog();
```

[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.

### <a name="tracesource-provider"></a>Poskytovatel TraceSource

Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá knihovny a poskytovatele <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.

Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core). Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je například <xref:System.Diagnostics.TextWriterTraceListener> používané v ukázkové aplikaci.

### <a name="azure-app-service-provider"></a>Poskytovatel Azure App Service

Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Balíček poskytovatele není zahrnutý ve sdíleném rozhraní. Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Pokud cílíte .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage, přidejte do projektu balíček poskytovatele. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Chcete-li nakonfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Chcete-li nakonfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Přetížení <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> umožňuje předat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru. (*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici s voláním metody `ILogger`.)

::: moniker-end

Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal. Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.

* **Protokolování aplikace (systém souborů)**
* **Protokolování aplikace (BLOB)**

Výchozí umístění souborů protokolu je ve složce *D:\\home\\* soubory protokolu\\složka aplikace a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*. Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2. Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.

Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure. Nemá žádný vliv, pokud se projekt spouští místně&mdash;nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.

#### <a name="azure-log-streaming"></a>Streamování protokolů Azure

Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:

* Aplikační server
* Webový server
* Trasování chybných požadavků

Konfigurace streamování protokolů Azure:

* Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .
* Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.
* Vyberte **úroveň**protokolu. Toto nastavení platí jenom pro streamování protokolů Azure, ne pro jiné poskytovatele protokolování v aplikaci.

Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** . Jsou protokolovány aplikací prostřednictvím rozhraní `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Protokolování trasování Azure Application Insights

Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights. Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie. Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.

Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core. Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.

Nepoužívejte balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , který je k dis&mdash;pro ASP.NET 4. x.

Další informace naleznete v následujících zdrojích:

* [Přehled Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.
* [ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.
* [Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.

## <a name="third-party-logging-providers"></a>Zprostředkovatelé protokolování třetích stran

Protokolovací architektury třetích stran, které pracují s ASP.NET Core:

* [elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([úložiště GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.NET](https://kisslog.net/) ([úložiště GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([úložiště GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLOG](https://nlog-project.org/) ([úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))

Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:

1. Přidejte do projektu balíček NuGet.
1. Zavolejte metodu rozšíření `ILoggerFactory` poskytovanou protokolovacím rozhraním.

Další informace najdete v dokumentaci pro každého poskytovatele. Microsoft nepodporuje zprostředkovatele protokolování třetích stran.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/logging/loggermessage>
