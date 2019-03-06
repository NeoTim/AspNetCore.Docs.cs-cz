---
title: Protokolování v ASP.NET Core
author: tdykstra
description: Další informace o protokolovacího rozhraní v ASP.NET Core. Objevte poskytovatelé vestavěné protokolování a další informace o Oblíbené zprostředkovatele třetí strany.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/02/2019
uid: fundamentals/logging/index
---
# <a name="logging-in-aspnet-core"></a>Protokolování v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [Petr Dykstra](https://github.com/tdykstra)

ASP.NET Core podporuje protokolování rozhraní API, která funguje s různých poskytovatelů třetích stran a vestavěné protokolování. Tento článek ukazuje, jak používat rozhraní API protokolování s předdefinované zprostředkovatele.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Přidat zprostředkovatele

Zprostředkovatel protokolování zobrazí nebo ukládají protokoly. Například konzola poskytovatel zobrazí protokoly v konzole a uloží je zprostředkovatel služby Azure Application Insights ve službě Azure Application Insights. Protokoly můžete odeslány do více cílů přidáním více poskytovatelů.

::: moniker range=">= aspnetcore-2.0"

Chcete-li přidat poskytovatele, zavolejte poskytovatele `Add{provider name}` metody rozšíření v *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

Volání výchozí projekt šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, který přidá následující zprostředkovatele protokolování:

* Konzola
* Ladit
* EventSource (od verze 2.2 technologie ASP.NET Core)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Pokud používáte `CreateDefaultBuilder`, výchozí poskytovatele můžete nahradit vlastními volbami. Volání <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, a přidat zprostředkovatele, který chcete.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Používat poskytovatele, nainstalujte svůj balíček NuGet a volání metody rozšíření zprostředkovatele na instanci <xref:Microsoft.Extensions.Logging.ILoggerFactory>:

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) poskytuje `ILoggerFactory` instance. `AddConsole` a `AddDebug` metody rozšíření jsou definovány v [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) a [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) balíčky. Každá metoda rozšíření volá `ILoggerFactory.AddProvider` metodu instance zprostředkovatele.

> [!NOTE]
> [Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) přidá poskytovatele protokolování v `Startup.Configure` metody. K získání výstupu protokolu z kódu, který se spustí dříve, přidejte poskytovatelů protokolování v `Startup` konstruktoru třídy.

::: moniker-end

Další informace o [vestavěné protokolování poskytovatelé](#built-in-logging-providers) a [zprostředkovatele přihlášení třetí strany](#third-party-logging-providers) dále v tomto článku.

## <a name="create-logs"></a>Vytvořit protokoly

Získat <xref:Microsoft.Extensions.Logging.ILogger`1> objekt z DI.

::: moniker range=">= aspnetcore-2.0"

Následující příklad řadič vytvoří `Information` a `Warning` protokoly. *Kategorie* je `TodoApiSample.Controllers.TodoController` (plně kvalifikovaný název třídy `TodoController` v ukázkové aplikaci):

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Následující příklad stránky Razor vytvoří protokolů pomocí `Information` jako *úroveň* a `TodoApiSample.Pages.AboutModel` jako *kategorie*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

V předchozím příkladu se vytvoří protokolů pomocí `Information` a `Warning` jako *úroveň* a `TodoController` třídy jako *kategorie*. 

::: moniker-end

Protokol *úroveň* označuje závažnost protokolované události. Protokol *kategorie* je řetězec, který je spojen s každou protokolu. `ILogger<T>` Instance vytvářejí protokoly, které mají plně kvalifikovaný název typu `T` kategorii. [Úrovně](#log-level) a [kategorie](#log-category) jsou vysvětlené podrobněji dále v tomto článku. 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a>Vytvořit protokoly v po spuštění

Zápis protokolů `Startup` třídy, patří `ILogger` parametr v konstruktoru podpis:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a>Vytvořit protokoly v aplikaci

Zápis protokolů `Program` třídy, získat `ILogger` instanci z DI:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Žádné metody na asynchronní protokolovací nástroj

Protokolování by měl být tak rychle, že se vyplatí výkon asynchronního kódu. Pokud vaše úložiště dat protokolování je pomalá, nemáte přímý zápis. Vezměte v úvahu zpočátku zápis zpráv protokolu do rychlého úložiště a pak později přesunout k úložišti pomalé. Protokolujte například, do fronty zpráv, která má číst a trvale uložena do úložiště pomalé jiným procesem.

## <a name="configuration"></a>Konfigurace

Zprostředkovatel konfigurace protokolování poskytuje jeden nebo více poskytovatelů konfigurace:

* Formáty souborů (INI, JSON a XML).
* Argumenty příkazového řádku.
* Proměnné prostředí.
* Objekty .NET v paměti.
* Nešifrované [manažera tajných](xref:security/app-secrets) úložiště.
* Uložení šifrovaného uživatelského, jako například [Azure Key Vault](xref:security/key-vault-configuration).
* Vlastní zprostředkovatelé (nainstalované nebo vytváření).

Například konfigurace protokolování běžně poskytované `Logging` části souborů s nastavením aplikace. Následující příklad ukazuje obsah typické *appsettings. Development.JSON* souboru:

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

`Logging` Vlastnost může mít `LogLevel` a protokolu vlastnosti zprostředkovatele (konzoly se zobrazí).

`LogLevel` Vlastnosti v části `Logging` Určuje minimální [úroveň](#log-level) k protokolování pro vybrané kategorie. V tomto příkladu `System` a `Microsoft` kategorie protokolu `Information` úroveň a všech ostatních protokolů na `Debug` úroveň.

Další vlastnosti v části `Logging` určit konkrétní zprostředkovatele protokolování. V příkladu se pro zprostředkovatele konzoly. Pokud poskytovatel podporuje [protokolu obory](#log-scopes), `IncludeScopes` označuje, zda jsou povolena. Vlastnost zprostředkovatele (například `Console` v příkladu) může také určit `LogLevel` vlastnost. `LogLevel` v části zprostředkovatele Určuje úrovně pro přihlášení pro daného poskytovatele.

Pokud úrovně jsou určené v `Logging.{providername}.LogLevel`, má přednost před nic nastavit `Logging.LogLevel`.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

`LogLevel` klíče představují názvy protokolů. `Default` Klíč platí do protokolů, které nejsou výslovně uvedena. Hodnota představuje [úrovně protokolování](#log-level) použitý pro daný protokol.

::: moniker-end

Informace o implementaci zprostředkovatele konfigurace najdete v tématu <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Ukázkový výstup protokolování

Ukázkový kód je znázorněno v předchozí části protokolů se objeví v konzole při spuštění aplikace z příkazového řádku. Tady je příklad výstupu konzoly:

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

Protokoly předchozích nevygenerovaly se tím, že požadavek HTTP Get na ukázkovou aplikaci na `http://localhost:5000/api/todo/0`.

Tady je příklad stejného protokolů, jak se objeví v okně ladění, když spustíte ukázkovou aplikaci v sadě Visual Studio:


```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Protokoly, které jsou vytvořené `ILogger` volání je znázorněno v předchozí části začínají řetězcem "TodoApi.Controllers.TodoController". Protokoly, které začínají řetězcem "Microsoft". kategorie jsou z kódu rozhraní framework ASP.NET Core. ASP.NET Core a kód aplikace používají stejné protokolování rozhraní API a poskytovatelů.

Zbývající část tohoto článku popisuje některé podrobnosti a možností pro protokolování.

## <a name="nuget-packages"></a>Balíčky NuGet

`ILogger` a `ILoggerFactory` rozhraní jsou v [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), a mají výchozí implementace pro ně [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategorie protokolu

Když `ILogger` je vytvořen objekt, *kategorie* je pro ni zadán. Kategorie je součástí každé zprávě protokolu vytvořené z této instance `Ilogger`. Kategorie může být libovolný řetězec, ale tato konvence je použití názvu třídy, jako je například "TodoApi.Controllers.TodoController".

Použití `ILogger<T>` zobrazíte `ILogger` instanci, která používá plně kvalifikovaný typ název `T` jako kategorie:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` je ekvivalentní volání `CreateLogger` názvem plně kvalifikovaný typ `T`.

## <a name="log-level"></a>Úroveň protokolování

Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu. Úroveň protokolu označuje závažnost nebo důležitost. Například můžete například napsat `Information` po skončení metody obvykle a protokolu `Warning` protokolu po návratu metody *404 Nenalezeno* stavový kód.

Následující kód vytvoří `Information` a `Warning` protokoly:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

V předchozím kódu je první parametr [protokolu událost s ID](#log-event-id). Druhý parametr je šablona zprávy s zástupné symboly pro argument hodnoty podle zbývající parametry metody. Parametry metody jsou vysvětleny v [zpráv šablonu části](#log-message-template) dále v tomto článku.

Metody, které obsahují v názvu metody na úrovni protokolu (například `LogInformation` a `LogWarning`) jsou [rozšiřující metody pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Tyto metody volat `Log` metodu, která přebírá `LogLevel` parametru. Můžete volat `Log` metoda přímo spíše než jeden z těchto metod rozšíření, ale syntaxe je poměrně složitý. Další informace najdete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a [rozšíření protokolovače zdrojový kód](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definuje následující úrovně protokolu zde seřazené od nejnižší a nejvyšší závažnost.

* Trasování = 0

  Pro informace, které je obvykle vhodné pouze pro ladění. Tyto zprávy mohou obsahovat citlivé aplikaci data a by nemělo být povoleno v produkčním prostředí. *Ve výchozím nastavení zakázané.*

* Ladění = 1

  Informace, které mohou být užitečné pro vývoj a ladění. Příklad: `Entering method Configure with flag set to true.` Povolit `Debug` úroveň zaznamená v produkčním prostředí pouze při řešení potíží, kvůli velkému počtu protokoly.

* Informace o = 2

  Pro sledování obecný tok z aplikace. Tyto protokoly mají obvykle dlouhodobé některá z hodnot. Příklad: `Request received for path /api/todo`

* Upozornění = 3

  Neobvyklé nebo neočekávaných událostí v aplikaci flow. Ty mohou obsahovat chyby nebo jinými podmínkami, které není způsobit, že aplikace přestane, ale může být nutné prozkoumat. Zpracované výjimky jsou běžné místo, kde můžete použít `Warning` úrovně protokolování. Příklad: `FileNotFoundException for file quotes.txt.`

* Chyba = 4

  Chyby a výjimky, které nelze zpracovat. Tyto zprávy označují selhání aktuální aktivitu nebo operace (jako je například aktuální požadavek HTTP), ne k selhání celé aplikace. Příklad zprávy protokolu: `Cannot insert record due to duplicate key violation.`

* Kritická = 5

  Chyby, které vyžadují okamžitou pozornost. Příklady: scénářům ztráty dat, volné místo na disku.

Pomocí úroveň protokolu můžete řídit, kolik výstup protokolu je zapsán do konkrétního úložiště média nebo zobrazení okna. Příklad:

* V produkčním prostředí, odeslat `Trace` prostřednictvím `Information` úrovně do úložiště dat svazku. Odeslat `Warning` prostřednictvím `Critical` na hodnotu data ukládat.
* Během vývoje, odeslat `Warning` prostřednictvím `Critical` do konzoly a přidejte `Trace` prostřednictvím `Information` při řešení potíží.

[Filtrování protokolu](#log-filtering) části dále v tomto článku vysvětluje, jak řídit které úrovně protokolu zpracovává zprostředkovatele.

ASP.NET Core zapisuje protokoly pro události rozhraní framework. Příklady protokolu dříve v tomto článku vyloučené protokoly níže `Information` úroveň, takže žádná `Debug` nebo `Trace` úrovně protokoly byly vytvořeny. Tady je příklad vytvořených spuštěním ukázkové aplikace nakonfigurovat tak, aby zobrazit protokoly konzoly `Debug` protokoly:

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

## <a name="log-event-id"></a>ID události protokolu

Můžete zadat všechny protokoly *ID události*. Ukázková aplikace dělá to pomocí místně definované `LoggingEvents` třídy:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

ID události přidruží sadu událostí. Všechny protokoly týkající se zobrazení seznamu položek na stránce může být například 1001.

Poskytovatel protokolování může ukládat ID události v poli ID ve zprávě protokolování nebo dokonce vůbec. Ladění zprostředkovatele nezobrazí ID událostí. Konzola poskytovatel zobrazuje ID událostí do hranatých závorek za kategorií:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Šablona zprávy protokolu

Každý protokol Určuje zpráv šablonu. Šablona zprávy může obsahovat zástupné znaky, pro které jsou zadány argumenty. Použijte názvy nahraďte zástupné symboly, nikoli čísla.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Pořadí zástupné symboly, nikoli jejich názvy, určuje, jaké parametry se používají k zadání jejich hodnot. V následujícím kódu Všimněte si, že názvy parametrů jsou mimo pořadí v šablonu zprávy:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Tento kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:

```
Parameter values: parm1, parm2
```

Protokolovacího rozhraní funguje tak, aby zprostředkovatelé protokolování můžete implementovat [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Samotné argumenty jsou předány do systému protokolování, ne jenom šablony formátovaná zpráva. Tyto informace umožňuje poskytovatelům protokolování pro uložení hodnoty parametrů jako pole. Předpokládejme například, protokolovacího nástroje metoda volání najdete takto:

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Pokud jste odesílali protokoly do služby Azure Table Storage, může mít každá entita Azure Table `ID` a `RequestTime` vlastnosti, které zjednodušuje dotazy na data protokolu. Dotaz můžete najít všechny protokoly v rámci konkrétní `RequestTime` rozsahu bez parsování časový limit textové zprávy.

## <a name="logging-exceptions"></a>Protokolování výjimek

Protokolovací nástroj metody mají přetížení, které umožňují předat výjimku, jako v následujícím příkladu:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Různí poskytovatelé zpracovat informace o výjimce různými způsoby. Tady je příklad výstupu ladění zprostředkovatele z výše uvedeném kódu.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrování protokolu

::: moniker range=">= aspnetcore-2.0"

Můžete zadat minimální úroveň protokolování pro konkrétního zprostředkovatele a kategorie nebo pro všechny poskytovatele nebo všechny kategorie. Všechny protokoly nižší než minimální úroveň nejsou předán tohoto poskytovatele, tak nemusíte získat zobrazení nebo uložené.

Chcete-li potlačit všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování. Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Vytvoření pravidla filtru v konfiguraci

Volání kódu šablony projektu `CreateDefaultBuilder` nastavit protokolování pro poskytovatele konzoly a ladění. `CreateDefaultBuilder` Metoda také nastaví protokolování hledat konfiguraci `Logging` oddílu, psát kód podobný tomuto:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

Konfigurační data Určuje minimální úrovně podle poskytovatele a kategorie, jako v následujícím příkladu:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Tento dokument JSON vytvoří šest pravidla filtru: jeden pro zprostředkovatele ladění, čtyři pro zprostředkovatele konzoly a pro všechny poskytovatele. Jedno pravidlo je vybrán pro každého zprostředkovatele při `ILogger` je vytvořen objekt.

### <a name="filter-rules-in-code"></a>Pravidla filtru v kódu

Následující příklad ukazuje, jak zaregistrovat pravidla filtrování v kódu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Druhá `AddFilter` Určuje zprostředkovatele, který ladění pomocí názvu typu. První `AddFilter` platí pro všechny poskytovatele, protože ji neurčuje, typ zprostředkovatele.

### <a name="how-filtering-rules-are-applied"></a>Jak pravidla filtrování se použijí

Konfigurační data a `AddFilter` kód zobrazený v předchozích ukázkách vytvořit pravidla je znázorněno v následující tabulce. Prvních šest pocházet z příklad konfigurace a poslední dva pocházejí z příkladu kódu.

| Číslo | Poskytovatel      | Kategorie, které začínají...          | Minimální úroveň protokolování |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Ladit         | Všechny kategorie                          | Informace o       |
| 2      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Upozornění           |
| 3      | Konzola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Ladit             |
| 4      | Konzola       | Microsoft.AspNetCore.Mvc.Razor          | Chyba             |
| 5      | Konzola       | Všechny kategorie                          | Informace o       |
| 6      | Všichni poskytovatelé | Všechny kategorie                          | Ladit             |
| 7      | Všichni poskytovatelé | Systém                                  | Ladit             |
| 8      | Ladit         | Microsoft                               | Trasování             |

Když `ILogger` je vytvořen objekt, `ILoggerFactory` objekt vybere jedno pravidlo na poskytovatele, který chcete použít pro tento protokolovač. Všechny zprávy, autorem `ILogger` instance jsou filtrovány podle vybrané pravidla. Nejspecifičtější pravidla pro každého zprostředkovatele a dvojice kategorie je vybrat z dostupných pravidla.

Následující požadovaný algoritmus se používá pro každého zprostředkovatele při `ILogger` se vytvoří pro danou kategorii:

* Vyberte všechna pravidla, které odpovídají zprostředkovateli nebo její alias. Pokud není nalezena žádná shoda, vyberte všechna pravidla s poskytovatelem služby prázdný.
* Z výsledků v předchozím kroku vyberte pravidla s nejdelší odpovídající předpona kategorie. Pokud není nalezena žádná shoda, vyberte všechna pravidla, které nechcete zadat kategorii.
* Pokud je vybraných víc pravidel, trvat, než **poslední** jeden.
* Pokud jsou vybraná žádná pravidla, použijte `MinimumLevel`.

S předchozím seznam pravidel, Předpokládejme, že vytvoříte `ILogger` objektu pro kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Ladění zprostředkovatele platí pravidla 1, 6 a 8. Pravidlo 8 není nejvíce specifické, tak, aby se byla vybrána.
* Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6. Pravidlo 3 není nejvíce specifické.

Výsledná `ILogger` instance odešle protokoly `Trace` úroveň a vyšší k poskytovateli ladění. Protokoly z `Debug` úrovně a novější se odesílají do konzoly zprostředkovatele.

### <a name="provider-aliases"></a>Aliasy poskytovatele

Každý poskytovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu.  Pro předdefinované zprostředkovatele použijte následující aliasy:

* Konzola
* Ladit
* EventLog
* AzureAppServices
* TraceSource
* EventSource

### <a name="default-minimum-level"></a>Výchozí minimální úroveň

Je k dispozici minimální úroveň nastavení, která se projeví pouze v případě, že žádná pravidla z konfigurace nebo kódu platí pro daného poskytovatele a kategorii. Následující příklad ukazuje, jak nastavit minimální úroveň:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Pokud není explicitně nastavena na minimální úroveň, výchozí hodnota je `Information`, což znamená, že `Trace` a `Debug` protokoly jsou ignorovány.

### <a name="filter-functions"></a>Funkce filtru

Pro všechny poskytovatele a kategorie, které nemají přiřazené konfigurace nebo kódu pravidla je vyvolána funkce filtru. Kód ve funkci má přístup k typu poskytovatele, kategorii a úroveň protokolu. Příklad:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Někteří poskytovatelé protokolování umožňují zadat při protokoly by měly být zapsána do úložiště média nebo ignorovat podle úroveň protokolu a kategorie.

`AddConsole` a `AddDebug` rozšiřující metody poskytují přetížení přijímající kritéria filtrování. Následující ukázkový kód způsobí, že konzola poskytovatel ignorovat protokolech níže `Warning` úroveň, při ladění zprostředkovatele ignoruje protokoly, které vytvoří rozhraní framework.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` Metoda má přetížení přijímající `EventLogSettings` instanci, která může obsahovat funkce filtrování v jeho `Filter` vlastnost. Zprostředkovatel TraceSource neposkytuje, některý z těchto přetížení, protože jeho úroveň protokolování a další parametry jsou založené na `SourceSwitch` a `TraceListener` používá.

Chcete-li nastavit pravidla filtrování pro všech zprostředkovatelů, které jsou registrovány `ILoggerFactory` použijte `WithFilter` – metoda rozšíření. Následující příklad omezuje framework protokoly (kategorie začíná řetězcem "Microsoft" nebo "Systém") a upozornění při protokolování na úrovni ladění pro protokoly vytvořené metodami kódu aplikace.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

Chcete-li zabránit zapisovaná všechny protokoly, zadejte `LogLevel.None` jako minimální úroveň protokolování. Celočíselnou hodnotu `LogLevel.None` je 6, která je vyšší než `LogLevel.Critical` (5).

`WithFilter` – Metoda rozšíření poskytuje [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) balíček NuGet. Metoda vrátí nový `ILoggerFactory` zaregistrovaný poskytovatel instanci, která bude filtrovat zprávy protokolu, který je předán všichni poskytovatelé protokolovacího nástroje. To nemá vliv na jiné `ILoggerFactory` instance, včetně původní `ILoggerFactory` instance.

::: moniker-end

## <a name="system-categories-and-levels"></a>Systém kategorií a úrovně

Tady jsou některé kategorie, která používá ASP.NET Core a Entity Framework Core s poznámky o co protokoly můžete očekávat od nich:

| Kategorie                            | Poznámky |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostika obecné ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Jaké byly považovat za, nalezen a používání klíčů. |
| Microsoft.AspNetCore.HostFiltering  | Hostitelé povolené. |
| Microsoft.AspNetCore.Hosting        | Jak dlouho požadavky HTTP trvalo dokončení a kdy se spouští. Po spuštění sestavení, hostování byly načteny. |
| Microsoft.AspNetCore.Mvc            | Diagnostika MVC a syntaxe Razor. Model, navázání, spuštění filtru, kompilace zobrazení výběr akce. |
| Microsoft.AspNetCore.Routing        | Trasu odpovídající informace. |
| Microsoft.AspNetCore.Server         | Připojení spuštění, zastavení a keep alive odpovědi. Informace o certifikátu HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Zpracování souborů. |
| Microsoft.EntityFrameworkCore       | Obecné Entity Framework Core diagnostiky. Aktivita a konfigurace, detekce změn migracemi databází. |

## <a name="log-scopes"></a>Protokol obory

 A *oboru* můžete seskupit sadu logických operací. Toto seskupení je možné se připojit k každý protokol, který je vytvořen jako součást sady stejná data. Každý protokol vytvořených jako součást zpracování transakcí může obsahovat třeba ID transakce.

Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metoda a bude trvat, dokud je uvolněn. Volání protokolovací nástroj pro zabalení použít obor `using` blok:

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Následující kód umožní obory pro zprostředkovatele konzoly:

::: moniker range="> aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.
>
> Informace o konfiguraci, najdete v článku [konfigurace](#configuration) oddílu.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Konfigurace `IncludeScopes` možnost protokolovací nástroj konzoly je nutné povolit protokolování na základě oboru.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

*Startup.cs*:

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

Každé zprávě protokolu obsahuje rozsahem informací:

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Poskytovatelé vestavěné protokolování

ASP.NET Core se celá dodává následující zprostředkovatele:

* [Console](#console-provider)
* [Ladění](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)

Možnosti pro [protokolování v Azure](#logging-in-azure) jsou popsané dále v tomto článku.

Informace o protokolování stdout najdete v tématu <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> a <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.

### <a name="console-provider"></a>Konzola zprostředkovatele

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) balíček zprostředkovatele odešle výstup protokolu konzoly. 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

[Přetížení AddConsole](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) umožňují předáním minimální úroveň protokolování, funkce filtru a logickou hodnotu, která označuje, zda jsou obory. Další možností je a zajistěte tak předání `IConfiguration` objektu, který můžete určit podporu obory a úrovní protokolování.

Zprostředkovatel konzoly má významný dopad na výkon a není obvykle vhodné pro použití v produkčním prostředí.

Když vytvoříte nový projekt v sadě Visual Studio `AddConsole` metoda vypadá takto:

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

Tento kód odkazuje `Logging` část *appSettings.json* souboru:

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

Nastavení zobrazené protokoly framework limit upozornění zároveň umožní aplikaci k přihlášení na úrovni ladění, jak je vysvětleno v [filtrování protokolu](#log-filtering) oddílu. Další informace najdete v tématu [konfigurace](xref:fundamentals/configuration/index).

::: moniker-end

Protokolování výstupu konzoly najdete ve složce projektu otevřete příkazový řádek a spusťte následující příkaz:

```console
dotnet run
```

### <a name="debug-provider"></a>Ladění zprostředkovatele

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) balíček zprostředkovatele zapíše výstup protokolu pomocí [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) třídy (`Debug.WriteLine` volání metody).

V systému Linux, tento zprostředkovatel zapisuje protokoly do */var/log/message*.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

[Přetížení AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) umožňují předáním minimální úroveň protokolování nebo funkce filtru.

::: moniker-end

### <a name="eventsource-provider"></a>Zprostředkovatel EventSource

Pro aplikace, které cílí ASP.NET Core 1.1.0 nebo novější, [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) balíček zprostředkovatele můžete implementovat trasování událostí. Na Windows, používá [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803). Zprostředkovatel je multiplatformní, ale nejsou žádná událost kolekce a zobrazení nástroje ještě pro Linux nebo macOS.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

Je dobrým způsobem, jak shromažďovat a zobrazovat protokoly použít [nástroje PerfView](https://github.com/Microsoft/perfview). Existují jiné nástroje pro prohlížení protokolů trasování událostí pro Windows, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows, protože ho vygeneroval technologie ASP.NET.

Ke konfiguraci PerfView pro shromažďování události zapsané podle tohoto zprostředkovatele, přidejte řetězec `*Microsoft-Extensions-Logging` k **dalších poskytovatelů** seznamu. (Nenechte si ujít hvězdičku na začátku řetězce.)

![Další zprostředkovatelé Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Poskytovatel protokolu událostí Windows

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) balíček zprostředkovatele odesílá výstup protokolu do protokolu událostí Windows.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předáním `EventLogSettings` nebo minimální úroveň protokolování.

::: moniker-end

### <a name="tracesource-provider"></a>TraceSource poskytovatele

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá balíček zprostředkovatele <xref:System.Diagnostics.TraceSource> knihovny a poskytovatelů.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předáním přepínač zdroje a naslouchací proces trasování.

K používání tohoto poskytovatele, musí aplikace spouštět rozhraní .NET Framework (spíše než .NET Core). Zprostředkovatel může směrovat zprávy širokou škálu [naslouchacích procesů](/dotnet/framework/debug-trace-profile/trace-listeners), například <xref:System.Diagnostics.TextWriterTraceListener> použít v ukázkové aplikaci.

::: moniker range="< aspnetcore-2.0"

Následující příklad nastaví `TraceSource` zprostředkovatele, který zaznamenává `Warning` a vyšší zprávu do okna konzoly.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a>Protokolování v Azure

Informace o protokolování v Azure najdete v následujících částech:

* [Zprostředkovatel služby Azure App Service](#azure-app-service-provider)
* [Streamování protokolů Azure](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [Protokolování trasování programu Azure Application Insights](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a>Zprostředkovatel služby Azure App Service

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) balíček zprostředkovatele zapisuje protokoly do textových souborů v systému souborů aplikace služby Azure App Service a na [úložiště objektů blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v účtu služby Azure Storage. Balíček zprostředkovatele je k dispozici pro aplikace zaměřené na .NET Core 1.1 nebo novější.

::: moniker range=">= aspnetcore-2.0"

Pokud je zaměřen na .NET Core, mějte na paměti následující body:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Balíček poskytovatel je součástí ASP.NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Balíček zprostředkovatele není součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app). Pokud chcete použít poskytovatele, nainstalujte balíček.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

* Nevolejte explicitně <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>. Zprostředkovatel je k dispozici do aplikace automaticky při nasazení aplikace do služby Azure App Service.

Pokud cílí na rozhraní .NET Framework nebo odkazující `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte do projektu balíček zprostředkovatele. Vyvolání `AddAzureWebAppDiagnostics`:

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Přetížení vám umožní předat v <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Objekt nastavení můžete přepsat výchozí nastavení, jako je například protokolování výstupu šablony, názvu objektu blob a limit velikosti souboru. (*Výstupu šablony* je zpráv šablonu, která se použije pro všechny protokoly kromě co je součástí `ILogger` volání metody.)

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Chcete-li nakonfigurovat nastavení poskytovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

Když nasadíte aplikaci služby App Service, aplikace respektuje nastavení v [diagnostické protokoly](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) část **služby App Service** stránky na webu Azure portal. Když tato nastavení jsou aktualizovány, změny se projeví okamžitě bez nutnosti restartování nebo opětovné nasazení aplikace.

![Nastavení protokolování Azure](index/_static/azure-logging-settings.png)

Výchozím umístěním pro soubory protokolů je *D:\\domácí\\LogFiles\\aplikace* složky a výchozí název souboru je *diagnostiky yyyymmdd.txt*. Výchozí limit velikosti souboru je 10 MB a výchozí maximální počet souborů, které uchovávají se 2. Výchozí název objektu blob je *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Zprostředkovatel funguje pouze v případě projektu běží v prostředí Azure. Nemá žádný vliv, pokud projekt je spuštěn místně&mdash;nelze zapsat do místních souborů nebo místním vývojovým úložištěm objektů BLOB.

### <a name="azure-log-streaming"></a>Streamování protokolů Azure

Streamování protokolů Azure umožňuje zobrazit protokol aktivit v reálném čase:

* Aplikační server
* Webový server
* Trasování neúspěšných žádostí

Postup konfigurace, streamování protokolů Azure:

* Přejděte **diagnostické protokoly** stránky na stránce portálu vaší aplikace.
* Nastavte **protokolování aplikace (systém souborů)** k **na**.

![Stránka portálu diagnostické protokoly Azure](index/_static/azure-diagnostic-logs.png)

Přejděte **streamování protokolů** stránku, abyste zobrazili zprávy aplikace. Jsou aplikace prostřednictvím přihlášení `ILogger` rozhraní.

![Streamování protokolů Azure aplikace na portálu.](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a>Protokolování trasování programu Azure Application Insights

Sada SDK služby Application Insights můžete shromažďovat a sestavy protokoly generované protokolování infrastruktury ASP.NET Core. Další informace naleznete v následujících materiálech:

* [Application Insights overview](/azure/application-insights/app-insights-overview)
* [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Protokolování adaptéry Application Insights](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).

::: moniker-end

## <a name="third-party-logging-providers"></a>Zprostředkovatele přihlášení třetí strany

Rozhraní protokolování třetích stran, které pracují s ASP.NET Core:

* [elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([úložiště GitHub se vzorovými](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](http://jsnlog.com/) ([úložiště GitHub se vzorovými](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))
* [Loggr](http://loggr.net/) ([úložiště GitHub se vzorovými](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](http://nlog-project.org/) ([úložiště GitHub se vzorovými](https://github.com/NLog/NLog.Extensions.Logging))
* [SENTRY](https://sentry.io/welcome/) ([úložiště GitHub se vzorovými](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([úložiště GitHub se vzorovými](https://github.com/serilog/serilog-extensions-logging))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště Github se vzorovými](https://github.com/googleapis/google-cloud-dotnet))

Můžete provádět některé rozhraní třetích stran [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Použití rozhraní třetích stran je podobný pomocí jedné z předdefinovaných poskytovatelů:

1. Přidání balíčku NuGet do projektu.
1. Volání `ILoggerFactory`.

Další informace najdete v dokumentaci každého zprostředkovatele. Zprostředkovatelů přihlášení třetích stran nejsou podporovány společností Microsoft.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/loggermessage>
