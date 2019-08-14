---
title: Přihlášení v .NET Core a ASP.NET Core
author: tdykstra
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 4e2aa1e18c3e3119e22452d5ca9b838581efbfd8
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994111"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Přihlášení v .NET Core a ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)

.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.

::: moniker range=">= aspnetcore-3.0"

Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích. Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host). Informace o tom, jak používat obecného hostitele v aplikacích nevyužívajících webovou konzolu, najdete v tématu [hostované služby](xref:fundamentals/host/hosted-services).

Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů. Příklady kódu, který není hostitelský, jsou uvedeny v těchto částech článku.

::: moniker-end

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Přidat zprostředkovatele

Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly. Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights. Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.

::: moniker range=">= aspnetcore-3.0"

Chcete-li přidat poskytovatele v aplikaci, která používá obecného hostitele, zavolejte metodu `Add{provider name}` rozšíření poskytovatele v *program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

V konzolové aplikaci, která není hostitelem, zavolejte metodu `Add{provider name}` rozšíření poskytovatele při `LoggerFactory`vytváření:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`a `AddConsole` vyžadují`using` příkaz pro `Microsoft.Extensions.Logging`.

Výchozí ASP.NET Core volání <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>šablon projektu, které přidává následující zprostředkovatele protokolování:

* Konzola
* Ladění
* EventSource
* Protokol událostí (pouze při spuštění v systému Windows)

Výchozí poskytovatele můžete nahradit vašimi vlastními možnostmi. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Chcete-li přidat poskytovatele, zavolejte metodu `Add{provider name}` rozšíření poskytovatele v *program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a. `Microsoft.Extensions.Configuration`

Výchozí volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>šablony projektu, které přidává následující zprostředkovatele protokolování:

* Konzola
* Ladění
* EventSource (počínaje ASP.NET Core 2,2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Pokud používáte `CreateDefaultBuilder`, můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>a přidejte poskytovatele, které chcete.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.

## <a name="create-logs"></a>Vytvořit protokoly

K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt. V rámci webové aplikace nebo hostované služby Získejte `ILogger` od vložení závislostí (di). V konzolových aplikacích bez hostitele použijte `LoggerFactory` k `ILogger`vytvoření.

Následující ASP.NET Core příklad vytvoří protokolovací nástroj s `TodoApiSample.Pages.AboutModel` jako kategorie. *Kategorie* protokolu je řetězec, který je spojený s každým protokolem. Instance poskytovaná di vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie. `ILogger<T>` 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Následující příklad nehostitelských konzolových aplikací vytvoří protokolovací nástroj s `LoggingConsoleApp.Program` jako kategorií.

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

Pokud chcete zapisovat protokoly ve `Program` třídě ASP.NET Core aplikace, Získejte po sestavení hostitele `ILogger` instanci od typu di:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

### <a name="create-logs-in-the-startup-class"></a>Vytvořit protokoly ve spouštěcí třídě

Pokud chcete zapisovat protokoly v `Startup.Configure` metodě ASP.NET Core aplikace, `ILogger` zahrňte do signatury metody parametr:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Zápis protokolů před dokončením nastavení di Container v `Startup.ConfigureServices` metodě není podporován:

* Vkládání protokolovacího nástroje `Startup` do konstruktoru není podporováno.
* Vkládání protokolovacího nástroje `Startup.ConfigureServices` do signatury metody není podporováno.

Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI. Kontejner di není nastavený, dokud `ConfigureServices` se nedokončí.

Vložení konstruktoru protokolovacího nástroje do `Startup` sady Works v dřívějších verzích ASP.NET Core, protože pro webového hostitele je vytvořen samostatný kontejner di. Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).

Pokud potřebujete nakonfigurovat službu, která závisí na `ILogger<T>`, můžete to provést pomocí injektáže konstruktoru nebo poskytnutím metody pro vytváření. Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost. Předpokládejme například, že potřebujete vyplnit vlastnost pomocí služby z DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Předchozí zvýrazněný kód je `Func` , který se spustí, když di Container potřebuje vytvořit `MyService`instanci. Tímto způsobem můžete získat přístup k libovolné registrované službě.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Vytvořit protokoly při spuštění

Chcete-li zapsat protokoly `Startup` ve třídě, `ILogger` zahrňte parametr do signatury konstruktoru:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Vytvořit protokoly ve třídě program

Chcete-li zapsat protokoly `Program` ve třídě, Získejte `ILogger` instanci od typu di:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Žádné metody asynchronního protokolovacího nástroje

Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu. Pokud je úložiště dat protokolování pomalé, nezapište ho přímo. Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště. Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, `Log` protože metody jsou synchronní. Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server.

## <a name="configuration"></a>Konfiguraci

Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:

* Formáty souborů (INI, JSON a XML).
* Argumenty příkazového řádku.
* Proměnné prostředí.
* Objekty rozhraní .NET v paměti.
* Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .
* Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno).

Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace. Následující příklad ukazuje obsah typického *appSettings. Soubor Development. JSON* :

::: moniker range=">= aspnetcore-2.1"

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

Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola). `Logging`

Vlastnost v rámci `Logging` určuje minimální úroveň protokolu pro vybrané kategorie. [](#log-level) `LogLevel` V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.

Další vlastnosti v `Logging` části zadat zprostředkovatele protokolování. Příklad je pro poskytovatele konzoly. Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny. Vlastnost poskytovatele (například `Console` v příkladu) může také `LogLevel` určovat vlastnost. `LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.

Pokud jsou úrovně specifikovány `Logging.{providername}.LogLevel`v, přepíše cokoli nastavené `Logging.LogLevel`v.

::: moniker-end

Informace o implementaci zprostředkovatelů konfigurace najdete v <xref:fundamentals/configuration/index>tématu.

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

Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0`.

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

Protokoly, které jsou vytvořené `ILogger` voláními, které jsou uvedené v předchozí části, začínají na "TodoApiSample". Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

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

Protokoly, které jsou vytvořené `ILogger` voláními, které jsou uvedené v předchozí části, začínají na "TodoApi". Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

::: moniker-end

Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.

## <a name="nuget-packages"></a>Balíčky NuGet

Rozhraní `ILogger` a`ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategorie protokolu

Při vytvoření objektu je pro něj zadána *kategorie.* `ILogger` Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger`. Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".

Použijte `ILogger<T>` k `T` získání instance, která používá plně kvalifikovaný název typu jako kategorii: `ILogger`

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Chcete-li explicitně zadat kategorii, `ILoggerFactory.CreateLogger`zavolejte:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným `T`názvem typu.

## <a name="log-level"></a>Úroveň protokolování

Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu. Úroveň protokolu označuje závažnost nebo důležitost. Můžete například zapsat `Information` protokol, pokud metoda končí normálně `Warning` a protokol, když metoda vrátí stavový kód 404 Nenalezeno.

Následující kód vytváří `Information` a `Warning` protokoluje:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

V předchozím kódu je prvním parametrem [ID události protokolu](#log-event-id). Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody. Parametry metody jsou vysvětleny v [části Šablona zprávy](#log-message-template) dále v tomto článku.

Metody protokolu, které zahrnují úroveň v názvu metody ( `LogInformation` například a `LogWarning`), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Tyto metody volají `Log` metodu, která `LogLevel` přebírá parametr. `Log` Metodu můžete zavolat přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá. Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.

ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.

* Trasování = 0

  Informace, které jsou obvykle užitečné pouze pro ladění. Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí. *Ve výchozím nastavení zakázáno.*

* Ladění = 1

  Informace, které mohou být užitečné při vývoji a ladění. Příklad: `Entering method Configure with flag set to true.`Povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.

* Informace = 2

  Pro sledování obecného toku aplikace. Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu. Příklad: `Request received for path /api/todo`

* Upozornění = 3

  Pro neobvyklé nebo neočekávané události v toku aplikace. Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat. Zpracování výjimek je běžné místo pro použití `Warning` úrovně protokolu. Příklad: `FileNotFoundException for file quotes.txt.`

* Chyba = 4

  Pro chyby a výjimky, které nelze zpracovat. Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace. Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`

* Kritické = 5

  Chyby, které vyžadují okamžitou pozornost. Příklady: scénáře ztráty dat, nedostatek místa na disku.

Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení. Příklad:

* V produkčním prostředí `Trace` můžete `Information` odesílat prostřednictvím úrovně úložiště dat svazku. Odešlete `Warning`doúložištědats hodnotou.`Critical`
* Během vývoje proveďte odeslání `Warning` `Critical` `Trace` do`Information` konzoly nástroje a při řešení potíží přidejte.

Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.

ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework. Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se nevytvořily žádné `Debug` protokoly ani `Trace` protokoly na úrovni. Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:

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
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu. Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.

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

Chcete-li potlačit všechny `LogLevel.None` protokoly, zadejte jako minimální úroveň protokolu. Celočíselná hodnota `LogLevel.None` je 6, což je více než `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Vytvořit pravidla filtru v konfiguraci

Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro konzolu a zprostředkovatele ladění. Metoda nastaví protokolování tak, aby hledalo konfiguraci `Logging` v části, jak je vysvětleno [výše v tomto článku](#configuration). `CreateDefaultBuilder`

Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele. Při `ILogger` vytvoření objektu se vybere jedno pravidlo pro každého poskytovatele.

### <a name="filter-rules-in-code"></a>Filtrovat pravidla v kódu

Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu. První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.

### <a name="how-filtering-rules-are-applied"></a>Jak se používají pravidla filtrování

Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce. Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.

| Číslo | Poskytovatel      | Kategorie, které začínají na...          | Minimální úroveň protokolování |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Ladění         | Všechny kategorie                          | Informace o       |
| 2      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Upozornění           |
| 3      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Ladění             |
| 4      | Konzola       | Microsoft.AspNetCore.Mvc.Razor          | Chyba             |
| 5      | Konzola       | Všechny kategorie                          | Informace o       |
| 6      | Všichni poskytovatelé | Všechny kategorie                          | Ladění             |
| 7      | Všichni poskytovatelé | Systém                                  | Ladění             |
| 8      | Ladění         | Microsoft                               | Přehled             |

`ILogger` Při vytvoření`ILoggerFactory` objektu vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj. Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel. V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.

Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:

* Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem. Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.
* Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie. Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.
* Pokud je vybráno více pravidel, vezměte **Poslední** z nich.
* Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel`.

V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor. RazorViewEngine":

* Pro poskytovatele ladění platí pravidla 1, 6 a 8. Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.
* Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6. Pravidlo 3 je nejvíce specifické.

Výsledná `ILogger` instance odesílá `Trace` protokoly úrovně a výše do poskytovatele ladění. `Debug` Protokoly úrovně a výše jsou odesílány poskytovateli konzoly.

### <a name="provider-aliases"></a>Aliasy zprostředkovatele

Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu.  Pro předdefinované poskytovatele použijte následující aliasy:

* Konzola
* Ladění
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

Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` protokoly a `Debug` budou ignorovány.

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
| Microsoft.AspNetCore                | Obecná diagnostika ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Které klíče byly zváženy, nalezeny a použity. |
| Microsoft.AspNetCore.HostFiltering  | Hostitelé povoleni. |
| Microsoft.AspNetCore.Hosting        | Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění. Která hostující spouštěcí sestavení byla načtena. |
| Microsoft.AspNetCore.Mvc            | Diagnostika MVC a Razor Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce. |
| Microsoft.AspNetCore.Routing        | Informace o shodě trasy. |
| Microsoft.AspNetCore.Server         | Připojení – spouštění, zastavování a udržování reakcí na Alive. Informace o certifikátu HTTPS |
| Microsoft.AspNetCore.StaticFiles    | Soubory byly obsluhovány. |
| Microsoft.EntityFrameworkCore       | Obecná diagnostika Entity Framework Core. Databázová aktivita a konfigurace, detekce změn, migrace. |

## <a name="log-scopes"></a>Rozsahy protokolů

 *Obor* může seskupit sadu logických operací. Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady. Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.

Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn. Použití oboru zabalením volání protokolovacího nástroje `using` v bloku:

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
> Aby bylo možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.`IncludeScopes`
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
* [Ladění](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>

### <a name="console-provider"></a>Poskytovatel konzoly

Balíček poskytovatele [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) odesílá výstup protokolu do konzoly. 

```csharp
logging.AddConsole();
```

Chcete-li zobrazit výstup protokolování konzoly, otevřete příkazový řádek ve složce projektu a spusťte následující příkaz:

```console
dotnet run
```

### <a name="debug-provider"></a>Poskytovatel ladění

Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` volání metody).

V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a>Zprostředkovatel EventSource

Pro aplikace cílené na ASP.NET Core 1.1.0 nebo novější může balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) implementovat trasování událostí. Ve Windows používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). Poskytovatel je pro různé platformy, ale pro Linux nebo macOS ještě nejsou k dispozici žádné nástroje pro shromažďování událostí a zobrazení.

```csharp
logging.AddEventSourceLogger();
```

Dobrým způsobem, jak shromažďovat a zobrazovat protokoly, je použít [Nástroj PerfView](https://github.com/Microsoft/perfview). Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.

Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte `*Microsoft-Extensions-Logging` řetězec do seznamu **další poskytovatelé** . (Na začátku řetězce nechybíš hvězdičku.)

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Zprostředkovatel událostí systému Windows

Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.

```csharp
logging.AddEventLog();
```

[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.

### <a name="tracesource-provider"></a>Poskytovatel TraceSource

Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.

Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core). Poskytovatel může směrovat zprávy na celou řadu posluchačů [](/dotnet/framework/debug-trace-profile/trace-listeners), jako je <xref:System.Diagnostics.TextWriterTraceListener> například použití v ukázkové aplikaci.

### <a name="azure-app-service-provider"></a>Poskytovatel Azure App Service

Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Balíček poskytovatele není zahrnutý ve sdíleném rozhraní. Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Při cílení na .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage přidejte do projektu balíček poskytovatele. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat nastavení zprostředkovatele <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> , <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>použijte a, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Chcete-li konfigurovat nastavení zprostředkovatele <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> , <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>použijte a, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení umožňuje <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>předat. Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru. (*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici `ILogger` při volání metody.)

::: moniker-end

Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal. Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.

* **Protokolování aplikace (systém souborů)**
* **Protokolování aplikace (BLOB)**

Výchozím umístěním pro soubory protokolu je složka *\\aplikace D:\\Home\\LogFiles* a výchozí název souboru je *Diagnostics-YYYYMMDD. txt*. Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2. Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/MM/DD/hh/{GUID}-applicationLog.txt*.

Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure. Nemá žádný vliv, pokud se projekt spouští místně&mdash;, nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.

#### <a name="azure-log-streaming"></a>Streamování protokolů Azure

Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:

* Aplikační server
* Webový server
* Trasování chybných požadavků

Konfigurace streamování protokolů Azure:

* Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .
* Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.
* Vyberte **úroveň**protokolu.

Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** . Protokolují ho aplikace přes `ILogger` rozhraní.

### <a name="azure-application-insights-trace-logging"></a>Protokolování trasování Azure Application Insights

Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights. Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie. Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.

Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core. Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.

Nepoužívejte&mdash;balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , který je pro ASP.NET 4. x.

Další informace naleznete v následujících materiálech:

* [Přehled Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pro ASP.NET Core aplikace](/azure/azure-monitor/app/asp-net-core) – začněte zde, pokud chcete implementovat celou řadu Application Insights telemetrie spolu s protokolováním.
* [ApplicationInsightsLoggerProvider pro protokoly .NET Core ILogger](/azure/azure-monitor/app/ilogger) – začněte sem, pokud chcete implementovat poskytovatele protokolování bez zbytku Application Insights telemetrie.
* [Application Insights adaptéry protokolování](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Instalace, konfigurace a inicializace Application Insights sady SDK](/learn/modules/instrument-web-app-code-with-application-insights) – Interaktivní kurz na webu Microsoft Learn.

## <a name="third-party-logging-providers"></a>Zprostředkovatelé protokolování třetích stran

Protokolovací architektury třetích stran, které pracují s ASP.NET Core:

* [elmah.IO](https://elmah.io/) ([úložiště GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [GELF](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Úložiště GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([Úložiště GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))
* [Loggr](https://loggr.net/) ([Úložiště GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLOG](https://nlog-project.org/) ([Úložiště GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([Úložiště GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([Úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))

Některé architektury třetích stran můžou provádět sémantické [protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:

1. Přidejte do projektu balíček NuGet.
1. `ILoggerFactory`Zavolejte.

Další informace najdete v dokumentaci pro každého poskytovatele. Microsoft nepodporuje zprostředkovatele protokolování třetích stran.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/loggermessage>
