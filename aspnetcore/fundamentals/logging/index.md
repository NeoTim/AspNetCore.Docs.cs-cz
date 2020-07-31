---
title: Protokolování v .NET Core a ASP.NET Core
author: rick-anderson
description: Naučte se používat protokolovací rozhraní poskytovanou balíčkem NuGet Microsoft. Extensions. Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330725"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Protokolování v .NET Core a ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Kirka Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) a [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.

Většina příkladů kódu, které jsou uvedené v tomto článku, se nachází v ASP.NET Corech aplikacích. Části těchto fragmentů kódu specifické pro protokolování se vztahují na libovolnou aplikaci .NET Core, která používá [obecného hostitele](xref:fundamentals/host/generic-host). Šablony ASP.NET Core webové aplikace používají obecného hostitele.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([Jak stáhnout](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Zprostředkovatelé protokolování

Zprostředkovatelé protokolování uchovávají protokoly, s výjimkou `Console` poskytovatele, který zobrazuje protokoly. Poskytovatel Azure Application Insights například ukládá protokoly v Azure Application Insights. Je možné povolit více zprostředkovatelů.

Výchozí ASP.NET Core šablony webové aplikace:

* Použijte [obecného hostitele](xref:fundamentals/host/generic-host).
* Zavolejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , čímž přidáte následující zprostředkovatele protokolování:
  * [Konzola](#console)
  * [Ladění](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): jenom Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

Předchozí kód ukazuje `Program` třídu vytvořenou pomocí šablon webové aplikace ASP.NET Core. Další části obsahují ukázky založené na šablonách ASP.NET Core webových aplikací, které používají obecného hostitele. Další v tomto dokumentu jsou popsány v [jiných než hostitelských konzolových aplikacích](#nhca) .

Chcete-li přepsat výchozí sadu zprostředkovatelů protokolování přidaných nástrojem `Host.CreateDefaultBuilder` , zavolejte `ClearProviders` a přidejte požadované poskytovatele protokolování. Například následující kód:

* Volání <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> pro odebrání všech <xref:Microsoft.Extensions.Logging.ILoggerProvider> instancí z tvůrce.
* Přidá zprostředkovatele protokolování [konzoly](#console) .

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Další poskytovatele najdete v těchto tématech:

* [Vestavění zprostředkovatelé protokolování](#bilp)
* [Zprostředkovatelé protokolování třetích stran](#third-party-logging-providers).

## <a name="create-logs"></a>Vytvořit protokoly

Chcete-li vytvořit protokoly, použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt z [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).

Následující příklad:

* Vytvoří protokolovací nástroj, `ILogger<AboutModel>` který používá *kategorii* protokolu plně kvalifikovaného názvu typu `AboutModel` . Kategorie protokolu je řetězec, který je spojený s každým protokolem.
* Volání <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> do protokolu na `Information` úrovni. *Úroveň* protokolu označuje závažnost protokolované události.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto dokumentu.

Informace o najdete v Blazor tématu [vytvoření protokolů v Blazor nástroji Blazor WebAssembly a](#clib) v tomto dokumentu.

[Při vytváření protokolů v nástroji Main a Startup](#clms) se dozvíte, jak vytvořit protokoly v `Main` a `Startup` .

## <a name="configure-logging"></a>Konfigurovat protokolování

Konfigurace protokolování je běžně poskytována `Logging` oddílem *appSettings*. `{Environment}` soubory *. JSON* . Následující *appsettings.Development.jsv* souboru vygeneruje šablony ASP.NET Core webové aplikace:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

V předchozím kódu JSON:

* `"Default"` `"Microsoft"` `"Microsoft.Hosting.Lifetime"` Jsou určeny kategorie, a.
* `"Microsoft"`Kategorie se vztahuje na všechny kategorie, které začínají na `"Microsoft"` . Toto nastavení se vztahuje například na `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` kategorii.
* `"Microsoft"`Protokol kategorie na úrovni protokolu `Warning` a vyšším.
* `"Microsoft.Hosting.Lifetime"`Kategorie je konkrétnější než `"Microsoft"` kategorie, takže se v `"Microsoft.Hosting.Lifetime"` kategorii protokoluje na úrovni protokolu "informace" a vyšší.
* Konkrétního poskytovatele protokolu není zadaný, takže `LogLevel` se použije na všechny povolené zprostředkovatele protokolování, s výjimkou protokolu [událostí systému Windows](#welog).

`Logging`Vlastnost může mít <xref:Microsoft.Extensions.Logging.LogLevel> vlastnosti a log Provider. `LogLevel`Určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie. V předchozím kódu JSON `Information` `Warning` jsou zadány úrovně protokolu. `LogLevel`označuje závažnost protokolu a rozsahů od 0 do 6:

`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, = `Error` 4, `Critical` = 5 a `None` = 6.

Je-li `LogLevel` zadán parametr, je protokolování povoleno pro zprávy na zadané úrovni a vyšší. V předchozím formátu JSON `Default` kategorie je protokolována `Information` a vyšší. Například zprávy, `Information` , `Warning` `Error` a `Critical` jsou protokolovány. Pokud `LogLevel` není zadaný, použije se jako výchozí `Information` úroveň protokolování. Další informace najdete v tématu [úrovně protokolu](#llvl).

Vlastnost zprostředkovatele může určovat `LogLevel` vlastnost. `LogLevel`v části poskytovatel Určuje úrovně, které se mají protokolovat pro daného zprostředkovatele, a přepíše nastavení protokolu bez poskytovatele. V souboru zvažte následující *appsettings.js* :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

Nastavení v `Logging.{providername}.LogLevel` Nastavení přepsání v `Logging.LogLevel` . V předchozím formátu JSON `Debug` je výchozí úroveň protokolu poskytovatele nastavená na `Information` :

`Logging:Debug:LogLevel:Default:Information`

Předchozí nastavení určuje `Information` úroveň protokolu pro každou `Logging:Debug:` kategorii s výjimkou `Microsoft.Hosting` . Když je uvedena konkrétní kategorie, konkrétní kategorie přepíše výchozí kategorii. V předchozím formátu JSON `Logging:Debug:LogLevel` Kategorie `"Microsoft.Hosting"` a `"Default"` přepisují nastavení v`Logging:LogLevel`

Minimální úroveň protokolu může být zadána pro některý z těchto:

* Konkrétní poskytovatelé: například`Logging:EventSource:LogLevel:Default:Information`
* Konkrétní kategorie: například`Logging:LogLevel:Microsoft:Warning`
* Všichni zprostředkovatelé a všechny kategorie:`Logging:LogLevel:Default:Warning`

Všechny protokoly pod minimální úrovní ***nejsou:***

* Předáno poskytovateli.
* Protokolováno nebo zobrazeno.

Pokud chcete potlačit všechny protokoly, zadejte [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None`má hodnotu 6, která je vyšší než `LogLevel.Critical` (5).

Pokud zprostředkovatel podporuje [obory protokolů](#logscopes), `IncludeScopes` označuje, zda jsou povoleny. Další informace najdete v tématu [rozsahy protokolů](#logscopes) .

Následující *appsettings.js* souboru obsahuje všechny poskytovatele povolené ve výchozím nastavení:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

V předchozí ukázce:

* Kategorie a úrovně nejsou navrhovanými hodnotami. Ukázka je k dispozici pro zobrazení všech výchozích zprostředkovatelů.
* Nastavení v `Logging.{providername}.LogLevel` Nastavení přepsání v `Logging.LogLevel` . Například úroveň v `Debug.LogLevel.Default` Přepisuje úroveň v `LogLevel.Default` .
* Použije se každý *alias* výchozího zprostředkovatele. Každý zprostředkovatel definuje *alias* , který lze použít v konfiguraci místo plně kvalifikovaného názvu typu. Předdefinovaná aliasy zprostředkovatelů jsou:
  * Konzola
  * Ladění
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Nastavení úrovně protokolu podle příkazového řádku, proměnných prostředí a jiné konfigurace

Úroveň protokolu může být nastavena libovolnými [poskytovateli konfigurace](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Následující příkazy:

* Nastavte klíč prostředí `Logging:LogLevel:Microsoft` na hodnotu `Information` ve Windows.
* Otestujte nastavení při použití aplikace vytvořené pomocí šablon webových aplikací ASP.NET Core. `dotnet run`Příkaz musí být spuštěn v adresáři projektu po použití `set` .

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Předchozí nastavení prostředí:

* Je nastaveno pouze v procesech spuštěných z příkazového okna, ve kterém byly nastaveny.
* Není přečteno prohlížeči spuštěným v aplikaci Visual Studio.

Následující příkaz [setx](/windows-server/administration/windows-commands/setx) taky nastavuje klíč a hodnotu prostředí ve Windows. Na rozdíl `set` od `setx` platí, že nastavení jsou trvalá. `/M`Přepínač nastaví proměnnou v prostředí systému. Pokud se `/M` nepoužívá, je nastavena proměnná prostředí uživatele.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

V [Azure App Service](https://azure.microsoft.com/services/app-service/)na stránce **Nastavení > konfigurace** vyberte **nové nastavení aplikace** . Azure App Service nastavení aplikace:

* Šifrované v klidovém stavu a přenášené přes zašifrovaný kanál.
* Vystaveno jako proměnné prostředí.

Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Další informace o nastavení hodnot konfigurace ASP.NET Core pomocí proměnných prostředí naleznete v tématu [proměnné prostředí](xref:fundamentals/configuration/index#environment-variables). Informace o používání dalších zdrojů konfigurace, včetně příkazového řádku, Azure Key Vault, konfigurace aplikace Azure, dalších formátů souborů a dalších, najdete v tématu <xref:fundamentals/configuration/index> .

## <a name="how-filtering-rules-are-applied"></a>Jak se používají pravidla filtrování

Při <xref:Microsoft.Extensions.Logging.ILogger%601> vytvoření objektu <xref:Microsoft.Extensions.Logging.ILoggerFactory> vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj. Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel. Z dostupných pravidel je vybráno pravidlo s nejpřesnější pro každou dvojici zprostředkovatelů a kategorií.

Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:

* Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem. Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.
* Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie. Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.
* Pokud je vybráno více pravidel, vezměte **Poslední** z nich.
* Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel` .

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Výstup protokolování z příkazu dotnet Run a Visual Studio

Zobrazí se protokoly vytvořené s [výchozím zprostředkovatelem protokolování](#lp) :

* V nástroji Visual Studio
  * V okně výstup ladění při ladění.
  * V okně ASP.NET Core webový server.
* V okně konzoly při spuštění aplikace s nástrojem `dotnet run` .

Protokoly, které začínají na kategoriích Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

<a name="lcat"></a>

## <a name="log-category"></a>Kategorie protokolu

Při `ILogger` vytvoření objektu je zadána *kategorie* . Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger` . Řetězec kategorie je libovolný, ale konvence používá název třídy. Například na řadiči může být název `"TodoApi.Controllers.TodoController"` . ASP.NET Core webové aplikace používají `ILogger<T>` k automatickému získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T` .

<a name="llvl"></a>

## <a name="log-level"></a>Úroveň protokolování

Následující tabulka uvádí <xref:Microsoft.Extensions.Logging.LogLevel> hodnoty, `Log{LogLevel}` způsob rozšíření pohodlí a navrhované použití:

| LogLevel | Hodnota | Metoda | Popis |
| -------- | ----- | ------ | ----------- |
| [Trasování](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Obsahuje nejpodrobnější zprávy. Tyto zprávy mohou obsahovat citlivá data aplikací. Tyto zprávy jsou ve výchozím nastavení zakázané a ***neměly by být*** povolené v produkčním prostředí. |
| [Ladění](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Pro ladění a vývoj. Používejte s opatrností v produkčním prostředí kvůli vysokému objemu. |
| [Informace](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Sleduje obecný tok aplikace. Může mít dlouhodobou hodnotu. |
| [Upozornění](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Pro neobvyklé nebo neočekávané události. Obvykle obsahuje chyby nebo podmínky, které nezpůsobí selhání aplikace. |
| [Chyba](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | Pro chyby a výjimky, které nelze zpracovat. Tyto zprávy označují selhání aktuální operace nebo žádosti, ne selhání celé aplikace. |
| [Kritické](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Chyby, které vyžadují okamžitou pozornost. Příklady: scénáře ztráty dat, nedostatek místa na disku. |
| [Žádný](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Určuje, že kategorie protokolování by neměla zapisovat žádné zprávy. |

V předchozí tabulce `LogLevel` je uveden od nejnižší k nejvyšší závažnosti.

První parametr metody [protokolu](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , označuje závažnost protokolu. Místo volání `Log(LogLevel, ...)` by většina vývojářů volala metody rozšíření [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) . `Log{LogLevel}`Rozšiřující metody [volají metodu protokolu a určují rozsah LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Například následující dvě volání protokolování jsou funkčně ekvivalentní a tvoří stejný protokol:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`je ID události. `MyLogEvents`je součástí ukázkové aplikace a zobrazí se v části [ID události protokolu](#leid) .

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Následující kód vytváří `Information` a `Warning` protokoluje:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

V předchozím kódu `Log{LogLevel}` je prvním parametrem `MyLogEvents.GetItem` [ID události protokolu](#leid). Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody. Parametry metody jsou vysvětleny v části [Šablona zprávy](#lmt) dále v tomto dokumentu.

Zavolejte příslušnou `Log{LogLevel}` metodu, která určuje, kolik výstupu protokolu se zapisuje na konkrétní paměťové médium. Příklad:

* V produkčním prostředí:
  * Protokolování na úrovni `Trace` nebo `Information` vytváří velké množství podrobných zpráv protokolu. Abyste mohli řídit náklady a nepřekračuje limity úložiště dat, protokolovat a vyhodnotit `Trace` `Information` zprávy na úrovni cloudového úložiště s nízkými náklady na velké objemy dat. Zvažte omezení `Trace` a `Information` konkrétní kategorie.
  * Přihlášení `Warning` prostřednictvím `Critical` úrovní by mělo mít za následek několik zpráv protokolu.
    * Náklady a limity úložiště většinou nejsou obavy.
    * Několik protokolů umožňuje větší flexibilitu v možnostech úložiště dat.
* Ve vývoji:
  * Nastavte na `Warning`.
  * Přidání `Trace` `Information` zpráv při řešení potíží. Pro omezení výstupu, nastavení `Trace` nebo `Information` pouze pro kategorie v rámci šetření.

ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework. Zvažte například výstup protokolu pro:

* RazorAplikace stránky vytvořená pomocí šablon ASP.NET Core.
* Protokolování je nastaveno na`Logging:Console:LogLevel:Microsoft:Information`
* Navigace na stránku ochrany osobních údajů:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

Následující sady JSON `Logging:Console:LogLevel:Microsoft:Information` :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>ID události protokolu

Každý protokol může určovat *ID události*. Ukázková aplikace používá `MyLogEvents` třídu k definování ID událostí:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

ID události přidružuje sadu událostí. Například všechny protokoly související se zobrazením seznamu položek na stránce může být 1001.

Zprostředkovatel protokolování může ukládat ID události v poli ID, ve zprávě protokolování nebo vůbec ne. Zprostředkovatel ladění nezobrazuje ID událostí. Poskytovatel konzoly zobrazuje ID událostí v závorkách za kategorií:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Někteří zprostředkovatelé protokolování ukládají ID události do pole, které umožňuje filtrování podle ID.

<a name="lmt"></a>

## <a name="log-message-template"></a>Šablona zprávy protokolu
<!-- Review, Each log API uses a message template. -->
Každé rozhraní API protokolu používá šablonu zprávy. Šablona zprávy může obsahovat zástupné symboly, pro které jsou k dispozici argumenty. Použijte názvy zástupných symbolů, nikoli čísla.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry slouží k zadání jejich hodnot. V následujícím kódu jsou názvy parametrů mimo pořadí v šabloně zprávy:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Předchozí kód vytvoří zprávu protokolu s hodnotami parametrů v pořadí:

```text
Parameter values: param1, param2
```

Tento přístup umožňuje zprostředkovatelům protokolování implementovat [sémantické nebo strukturované protokolování](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Samotné argumenty jsou předány do protokolovacího systému, nikoli pouze ze šablony formátované zprávy. To umožňuje zprostředkovatelům protokolování ukládat hodnoty parametrů jako pole. Zvažte například následující metodu protokolovacího nástroje:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Například při protokolování do Azure Table Storage:

* Každá entita tabulky Azure může mít `ID` `RequestTime` vlastnosti a.
* Tabulky s vlastnostmi zjednodušují dotazy na data protokolu. Dotaz může například najít všechny protokoly v rámci určitého `RequestTime` rozsahu bez nutnosti analyzovat časový limit textové zprávy.

## <a name="log-exceptions"></a>Protokolovat výjimky

Metody protokolovacího nástroje mají přetížení, která přijímají parametr výjimky:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Protokolování výjimek je specifické pro konkrétního zprostředkovatele.

### <a name="default-log-level"></a>Výchozí úroveň protokolování

Pokud není nastavená výchozí úroveň protokolování, je výchozí hodnota úrovně protokolu `Information` .

Zvažte například následující webovou aplikaci:

* Vytvořeno pomocí šablon webové aplikace ASP.NET.
* *appsettings.js* a *appsettings.Development.jspři* odstranění nebo přejmenování.

V předchozím nastavení se při přechodu na soukromí nebo na domovské stránce `Trace` `Debug` `Information` v názvu kategorie vytvoří mnoho zpráv, a `Microsoft` .

Následující kód nastaví výchozí úroveň protokolování, pokud výchozí úroveň protokolu není nastavena v konfiguraci:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
Obecně platí, že úrovně protokolu by měly být zadány v konfiguraci a nikoli kódu.

### <a name="filter-function"></a>Filter – funkce

Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Předchozí kód zobrazí protokoly konzoly, pokud kategorie obsahuje `Controller` nebo `Microsoft` a úroveň protokolu je `Information` nebo vyšší.

Obecně platí, že úrovně protokolu by měly být zadány v konfiguraci a nikoli kódu.

## <a name="aspnet-core-and-ef-core-categories"></a>Kategorie ASP.NET Core a EF Core

Následující tabulka obsahuje některé kategorie používané ASP.NET Core a Entity Framework Core s poznámkami k protokolům:

| Kategorie                            | Poznámky |
| ----------------------------------- | ----- |
| Microsoft. AspNetCore                | Obecná diagnostika ASP.NET Core. |
| Microsoft. AspNetCore. DataProtection | Které klíče byly zváženy, nalezeny a použity. |
| Microsoft. AspNetCore. HostFiltering  | Hostitelé povoleni. |
| Microsoft. AspNetCore. hosting        | Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění. Která hostující spouštěcí sestavení byla načtena. |
| Microsoft. AspNetCore. Mvc            | MVC a Razor Diagnostika. Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce. |
| Microsoft. AspNetCore. Routing        | Informace o shodě trasy. |
| Microsoft. AspNetCore. Server         | Připojení – spouštění, zastavování a udržování reakcí na Alive. Informace o certifikátu HTTPS |
| Microsoft. AspNetCore. StaticFiles    | Soubory byly obsluhovány. |
| Microsoft. EntityFrameworkCore       | Obecná diagnostika Entity Framework Core. Databázová aktivita a konfigurace, detekce změn, migrace. |

Chcete-li zobrazit více kategorií v okně konzoly, nastavte **appsettings.Development.jsna** následující:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Rozsahy protokolů

 *Obor* může seskupit sadu logických operací. Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady. Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.

Rozsah:

* Je <xref:System.IDisposable> typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou.
* Potrvá, dokud nebude zrušeno.

Následující poskytovatelé podporují obory:

* `Console`
* [AzureAppServicesFile a AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Použití oboru zabalením volání protokolovacího nástroje v `using` bloku:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Následující JSON umožňuje obory pro poskytovatele konzoly:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Následující kód umožňuje obory pro poskytovatele konzoly:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

Obecně platí, že by protokolování mělo být specifikováno v konfiguraci a nikoli kódu.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Vestavění zprostředkovatelé protokolování

ASP.NET Core obsahuje následující zprostředkovatele protokolování:

* [Konzola](#console)
* [Ladění](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile a AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Informace o `stdout` a ladění protokolování pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

### <a name="console"></a>Konzola

`Console`Zprostředkovatel protokoluje výstup do konzoly. Další informace o zobrazení `Console` protokolů ve vývoji najdete v tématu [protokolování výstupu z dotnet Run a Visual Studio](#dnrvs).

### <a name="debug"></a>Ladění

`Debug`Zprostředkovatel zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) . Volání pro `System.Diagnostics.Debug.WriteLine` zápis do `Debug` poskytovatele.

V systému Linux `Debug` je umístění protokolu zprostředkovatele závislé na distribuci a může to být jedna z následujících:

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Zdroj události

`EventSource`Zprostředkovatel zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging` . Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>nástroje pro trasování dotnet

Nástroj [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) je globální nástroj CLI pro různé platformy, který umožňuje shromažďování trasování .NET Core pro běžící proces. Nástroj shromažďuje <xref:Microsoft.Extensions.Logging.EventSource> data zprostředkovatele pomocí <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .

Pokyny k instalaci naleznete v tématu [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) .

Shromažďování trasování z aplikace pomocí trasovacích nástrojů dotnet:

1. Spusťte aplikaci pomocí `dotnet run` příkazu.
1. Určení identifikátoru procesu (PID) aplikace .NET Core:
   * Ve Windows použijte některý z následujících přístupů:
     * Správce úloh (Ctrl + Alt + Del)
     * [tasklist – příkaz](/windows-server/administration/windows-commands/tasklist)
     * [Příkaz PowerShellu Get-Process](/powershell/module/microsoft.powershell.management/get-process)
   * V systému Linux použijte [příkaz pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Vyhledejte kód PID pro proces, který má stejný název jako sestavení aplikace.

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

   Při použití příkazového prostředí PowerShellu uzavřete `--providers` hodnotu do jednoduchých uvozovek ( `'` ):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformách jiných než Windows přidejte `-f speedscope` možnost změny formátu výstupní trasovacího souboru na `speedscope` .

   | Klíčové slovo | Popis |
   | :-----: | ----------- |
   | 1       | Protokoluje meta události týkající se `LoggingEventSource` . Neprotokoluje události z `ILogger` ). |
   | 2       | Zapne `Message` událost při `ILogger.Log()` volání metody. Poskytuje informace v programovém (neformátovaném) způsobu. |
   | 4       | Zapne `FormatMessage` událost při `ILogger.Log()` volání metody. Poskytuje formátovanou verzi řetězce informací. |
   | 8       | Zapne `MessageJson` událost při `ILogger.Log()` volání metody. Poskytuje reprezentace argumentů ve formátu JSON. |

   | Úroveň události | Popis     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`položky pro `{Logger Category}` a `{Event Level}` reprezentují další podmínky filtrování protokolů. Jednotlivé `FilterSpecs` položky oddělte středníkem ( `;` ).

   Příklad použití příkazového prostředí systému Windows (**bez** jednoduchých uvozovek kolem `--providers` hodnoty):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Předchozí příkaz se aktivuje:

   * Protokolovací nástroj zdroje událostí pro vytváření formátovaných řetězců ( `4` ) pro chyby ( `2` ).
   * `Microsoft.AspNetCore.Hosting`protokolování na `Informational` úrovni protokolování ( `4` ).

1. Kliknutím na klávesu ENTER nebo stisknutím kombinace kláves CTRL + C zastavte nástroje pro trasování dotnet.

   Trasování je uloženo s názvem *Trace. nettrace* ve složce, ve které `dotnet trace` je příkaz spuštěn.

1. Otevřete trasování pomocí [PerfView](#perfview). Otevřete soubor *Trace. nettrace* a prozkoumejte události trasování.

Pokud aplikace nevytváří hostitele pomocí `CreateDefaultBuilder` , přidejte [poskytovatele zdroje událostí](#event-source-provider) do konfigurace protokolování aplikace.

Další informace naleznete v tématu:

* [Trasování pro nástroj Analýza výkonu (dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentace k .NET Core)
* [Trasování pro nástroj Analýza výkonu (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentace k úložišti GitHub/Diagnostika)
* [LoggingEventSource – třída](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (prohlížeč rozhraní .NET API)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource reference Source (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Chcete-li získat zdroj odkazů pro jinou verzi, změňte větev na `release/{Version}` , kde `{Version}` je požadovaná verze ASP.NET Core.
* [PerfView](#perfview): užitečné pro zobrazení trasování zdroje událostí.

#### <a name="perfview"></a>PerfView

K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) . Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.

Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **další poskytovatelé** . Nechybíš na `*` začátku řetězce.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Protokol událostí systému Windows

`EventLog`Zprostředkovatel odešle výstup protokolu do protokolu událostí systému Windows. Na rozdíl od jiných zprostředkovatelů `EventLog` nedědí zprostředkovatel ***not*** výchozí nastavení bez zprostředkovatele. Pokud `EventLog` není nastavení protokolu zadané, použijí se ve [výchozím nastavení možnost LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Chcete-li protokolovat události nižší než <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , explicitně nastavte úroveň protokolu. Následující příklad nastaví výchozí úroveň protokolu událostí na <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[Přetížení AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) můžou být předána <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Pokud `null` není zadaný, použijí se následující výchozí nastavení:

* `LogName`: "Application"
* `SourceName`: ".NET runtime"
* `MachineName`: Používá se název místního počítače.

Následující kód změní `SourceName` výchozí hodnotu z na `".NET Runtime"` `MyLogs` :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.

Balíček poskytovatele není zahrnutý ve sdíleném rozhraní. Chcete-li použít poskytovatele, přidejte do projektu balíček poskytovatele.

Chcete-li konfigurovat nastavení zprostředkovatele, použijte <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> a <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> , jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Při nasazení do Azure App Service aplikace používá nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) stránky **App Service** Azure Portal. Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.

* **Protokolování aplikace (systém souborů)**
* **Protokolování aplikace (BLOB)**

Výchozí umístění souborů protokolu je ve složce *D: Home program \\ \\ Logfiles \\ * a výchozí název souboru je *diagnostics-yyyymmdd.txt*. Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2. Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.

Tento poskytovatel se zaprotokoluje, jenom když se projekt spustí v prostředí Azure.

#### <a name="azure-log-streaming"></a>Streamování protokolů Azure

Streamování protokolů Azure podporuje zobrazení aktivity protokolu v reálném čase z:

* Aplikační server
* Webový server
* Trasování chybných požadavků

Konfigurace streamování protokolů Azure:

* Na stránce portálu aplikace přejděte na stránku **protokoly App Service** .
* Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.
* Vyberte **úroveň**protokolu. Toto nastavení platí jenom pro streamování protokolů Azure.

Pokud chcete zobrazit protokoly, přejděte na stránku **streamu protokolu** . Protokolované zprávy jsou protokolovány pomocí `ILogger` rozhraní.

### <a name="azure-application-insights"></a>Azure Application Insights

Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do [Azure Application Insights](/azure/azure-monitor/app/cloudservices). Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie. Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.

Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core. Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.

Balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) je určen pro ASP.NET 4. x, nikoli ASP.NET Core.

Další informace naleznete v následujících zdrojích:

* [Přehled služby Application Insights](/azure/application-insights/app-insights-overview)
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
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([úložiště GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([úložiště GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([úložiště GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([úložiště GitHub](https://github.com/googleapis/google-cloud-dotnet))

Některé architektury třetích stran můžou provádět [sémantické protokolování, označované taky jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Použití architektury třetí strany se podobá použití jednoho z vestavěných zprostředkovatelů:

1. Přidejte do projektu balíček NuGet.
1. Zavolejte `ILoggerFactory` metodu rozšíření poskytovanou protokolovacím rozhraním.

Další informace najdete v dokumentaci pro každého poskytovatele. Microsoft nepodporuje zprostředkovatele protokolování třetích stran.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Konzolová aplikace bez hostitele

Příklad použití obecného hostitele v aplikaci jiné než webové konzoly najdete v souboru *program.cs* [ukázkové aplikace úlohy na pozadí](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).

Kód protokolování pro aplikace bez obecného hostitele se liší v způsobu [Přidání zprostředkovatelů](#add-providers) a [vytváření protokolovacích](#create-logs)nástrojů. 

### <a name="logging-providers"></a>Zprostředkovatelé protokolování

V konzolové aplikaci, která není hostitelem, zavolejte `Add{provider name}` metodu rozšíření poskytovatele při vytváření `LoggerFactory` :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Vytvořit protokoly

K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt. Použijte `LoggerFactory` k vytvoření `ILogger` .

Následující příklad vytvoří protokolovací `LoggingConsoleApp.Program` Nástroj s jako kategorie.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

V následujících příkladech ASP.NET základního nástroje se k vytváření protokolů s jako úroveň používá protokolovací nástroj `Information` . *Úroveň* protokolu označuje závažnost protokolované události.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny v tomto dokumentu.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Přihlášení během konstrukce hostitele

Protokolování během konstrukce hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateHostBuilder` . `AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger` :

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Konfigurace služby, která závisí na ILogger

Vložení konstruktoru protokolovacího nástroje do `Startup` sady Works v dřívějších verzích ASP.NET Core, protože pro webového hostitele je vytvořen samostatný kontejner di. Informace o tom, proč je pro obecného hostitele vytvořeno jenom jeden kontejner, najdete v části [oznámení o změně](https://github.com/aspnet/Announcements/issues/353).

Chcete-li nakonfigurovat službu, která závisí na `ILogger<T>` , použijte injektáže konstruktoru nebo poskytněte metodu pro vytváření. Přístup k metodě pro vytváření je doporučen pouze v případě, že není k dispozici žádná jiná možnost. Představte si třeba službu, která potřebuje `ILogger<T>` instanci poskytovanou výrazem di:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Předchozí zvýrazněný kód je [Func](/dotnet/api/system.func-2) , který se spouští při prvním, kdy musí kontejner di vytvořit instanci `MyService` . Tímto způsobem můžete získat přístup k libovolné registrované službě.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Vytváření protokolů v Main

Následující kód se přihlásí po `Main` získání `ILogger` instance z di po sestavení hostitele:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Vytvořit protokoly při spuštění

Následující kód zapisuje protokoly v `Startup.Configure` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

Zápis protokolů před dokončením nastavení DI Container v metodě není `Startup.ConfigureServices` podporován:

* Vkládání protokolovacího nástroje do `Startup` konstruktoru není podporováno.
* Vkládání protokolovacího nástroje do `Startup.ConfigureServices` signatury metody není podporováno.

Důvodem tohoto omezení je, že protokolování závisí na DI a na konfiguraci, která v systému zapíná na DI. Kontejner DI není nastavený, dokud se `ConfigureServices` nedokončí.

Informace o konfiguraci služby, která závisí na `ILogger<T>` nebo proč konstruktor, který se má `Startup` přepracovat v dřívějších verzích, najdete v tématu [Konfigurace služby, která závisí na ILogger](#csdi) .

### <a name="no-asynchronous-logger-methods"></a>Žádné metody asynchronního protokolovacího nástroje

Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu. Pokud je úložiště dat protokolování pomalé, nezapište ho přímo. Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je přesunout do pomalého úložiště později. Například když se přihlásíte k SQL Server, neprovádějte přímo v `Log` metodě, protože `Log` metody jsou synchronní. Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server. Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Změna úrovní protokolu v běžící aplikaci

Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná. Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování. Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider)znovu načte konfiguraci protokolování do výchozího nastavení. Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.

## <a name="ilogger-and-iloggerfactory"></a>ILogger a ILoggerFactory

<xref:Microsoft.Extensions.Logging.ILogger%601>Rozhraní a <xref:Microsoft.Extensions.Logging.ILoggerFactory> implementace jsou součástí .NET Core SDK. Jsou také k dispozici v následujících balíčcích NuGet:  

* Rozhraní jsou v [Microsoft. Extensions. Logging. abstrakce](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Výchozí implementace jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Použít pravidla filtru protokolů v kódu

Upřednostňovaným přístupem k nastavení pravidel filtru protokolů je použití [Konfigurace](xref:fundamentals/configuration/index).

Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)`Určuje `System` kategorii a úroveň protokolu `Debug` . Filtr se použije pro všechny poskytovatele, protože nebyl nakonfigurovaný konkrétní zprostředkovatel.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`upřesňuj

* `Debug`Zprostředkovatel protokolování.
* Úroveň protokolu `Information` a vyšší.
* Všechny kategorie počínaje `"Microsoft"` .

## <a name="create-a-custom-logger"></a>Vytvoření vlastního protokolovacího nástroje

K přidání vlastního protokolovacího nástroje přidejte <xref:Microsoft.Extensions.Logging.ILoggerProvider> <xref:Microsoft.Extensions.Logging.ILoggerFactory> :

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider`Vytvoří jednu nebo více `ILogger` instancí. `ILogger`Instance používá rozhraní k protokolování informací.

### <a name="sample-custom-logger-configuration"></a>Ukázka vlastní konfigurace protokolovacího nástroje

Ukázka:

* Je navržený jako velmi základní vzorek, který nastavuje barvu konzoly protokolu podle ID události a úrovně protokolu. Protokolovací nástroje se obecně nemění podle ID události a nevztahují se na úroveň protokolu.
* Vytvoří různé položky konzoly barev na úrovni protokolu a ID události s použitím následujícího typu konfigurace:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Předchozí kód nastaví výchozí úroveň na `Warning` a barvu na `Yellow` . Pokud `EventId` je nastavené na 0, protokolují se všechny události.

### <a name="create-the-custom-logger"></a>Vytvoření vlastního protokolovacího nástroje

`ILogger`Název kategorie implementace je obvykle zdrojem protokolování. Například typ, ve kterém je vytvořen protokolovací nástroj:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Předcházející kód:

* Vytvoří instanci protokolovacího nástroje pro název kategorie.
* Vrátí `logLevel == _config.LogLevel` `IsEnabled` se změnami, takže každá z nich `logLevel` má jedinečný protokolovací nástroj. Obecně platí, že protokolovací nástroje by měly být povolené i pro všechny vyšší úrovně protokolu:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Vytvoření vlastního LoggerProvider

`LoggerProvider`Je třída, která vytváří instance protokolovacího nástroje. Možná není nutné vytvořit instanci protokolovacího nástroje na kategorii, ale to dává smysl pro některé protokolovací nástroje, jako je NLog nebo log4net. V případě potřeby také můžete zvolit různé cíle pro výstup protokolování na kategorii:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

V předchozím kódu <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> vytvoří jednu instanci pro `ColorConsoleLogger` název kategorie a uloží ji do [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;

### <a name="usage-and-registration-of-the-custom-logger"></a>Využití a registrace vlastního protokolovacího nástroje

Zaregistrujte protokolovací nástroj v `Startup.Configure` :

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Pro předchozí kód zadejte alespoň jednu metodu rozšíření pro `ILoggerFactory` :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/loggermessage>
* V úložišti [GitHub.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) by se měly vytvářet chyby protokolování.
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tím, že [Dykstra](https://github.com/tdykstra) a [Steve Smith](https://ardalis.com/)

.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců. V tomto článku se dozvíte, jak používat rozhraní API protokolování s integrovanými poskytovateli.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Přidat zprostředkovatele

Zprostředkovatel protokolování zobrazuje nebo ukládá protokoly. Například poskytovatel konzoly zobrazuje protokoly v konzole nástroje a poskytovatel Azure Application Insights je ukládá do Azure Application Insights. Protokoly lze odesílat do více cílů přidáním více zprostředkovatelů.

Chcete-li přidat poskytovatele, zavolejte `Add{provider name}` metodu rozšíření poskytovatele v *program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Předchozí kód vyžaduje odkazy na `Microsoft.Extensions.Logging` a `Microsoft.Extensions.Configuration` .

Výchozí volání šablony projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , které přidává následující zprostředkovatele protokolování:

* Konzola
* Ladění
* EventSource (počínaje ASP.NET Core 2,2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Pokud používáte `CreateDefaultBuilder` , můžete výchozí poskytovatele nahradit vašimi vlastními možnostmi. Zavolejte <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> a přidejte poskytovatele, které chcete.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Přečtěte si další informace o [integrovaných poskytovatelích protokolování](#built-in-logging-providers) a [zprostředkovatelích protokolování třetích stran](#third-party-logging-providers) v tomto článku.

## <a name="create-logs"></a>Vytvořit protokoly

K vytváření protokolů použijte <xref:Microsoft.Extensions.Logging.ILogger%601> objekt. V rámci webové aplikace nebo hostované služby Získejte `ILogger` od vložení závislostí (di). V konzolových aplikacích bez hostitele použijte `LoggerFactory` k vytvoření `ILogger` .

Následující ASP.NET Core příklad vytvoří protokolovací `TodoApiSample.Pages.AboutModel` Nástroj s jako kategorie. *Kategorie* protokolu je řetězec, který je spojený s každým protokolem. `ILogger<T>`Instance poskytovaná di vytvoří protokoly, které mají plně kvalifikovaný název typu `T` jako kategorie. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

V následujících příkladech ASP.NET Core a konzolových aplikací se k vytváření protokolů s jako úroveň používá protokolovací nástroj `Information` . *Úroveň* protokolu označuje závažnost protokolované události.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Úrovně](#log-level) a [kategorie](#log-category) jsou podrobněji vysvětleny dále v tomto článku.

### <a name="create-logs-in-startup"></a>Vytvořit protokoly při spuštění

Chcete-li zapsat protokoly ve `Startup` třídě, zahrňte `ILogger` parametr do signatury konstruktoru:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Vytvořit protokoly ve třídě program

Chcete-li zapsat protokoly ve `Program` třídě, Získejte `ILogger` instanci od typu di:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Protokolování během konstrukce hostitele není přímo podporováno. Lze však použít samostatný protokolovací nástroj. V následujícím příkladu se k přihlášení používá protokolovací nástroj [Serilog](https://serilog.net/) `CreateWebHostBuilder` . `AddSerilog`používá statickou konfiguraci zadanou v `Log.Logger` :

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

### <a name="no-asynchronous-logger-methods"></a>Žádné metody asynchronního protokolovacího nástroje

Protokolování by mělo být tak rychlé, aby neplatilo náklady na výkon asynchronního kódu. Pokud je úložiště dat protokolování pomalé, nezapište ho přímo. Nejprve zvažte možnost zapsat zprávy protokolu do rychlého úložiště a pak je později přesunout do pomalého úložiště. Například pokud se přihlašujete k SQL Server, nechcete provádět přímo v `Log` metodě, protože `Log` metody jsou synchronní. Místo toho můžete synchronně přidat protokolové zprávy do fronty v paměti a nechat pracovní proces na pozadí získat zprávy z fronty, aby asynchronní operace vkládání dat do SQL Server. Další informace najdete v [tomto](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problému GitHubu.

## <a name="configuration"></a>Konfigurace

Konfigurace zprostředkovatele protokolování je poskytována jedním nebo více poskytovateli konfigurace:

* Formáty souborů (INI, JSON a XML).
* Argumenty příkazového řádku.
* Proměnné prostředí.
* Objekty rozhraní .NET v paměti.
* Úložiště nešifrovaného [tajného správce](xref:security/app-secrets) .
* Šifrované uživatelské úložiště, například [Azure Key Vault](xref:security/key-vault-configuration).
* Vlastní poskytovatelé (nainstalováno nebo vytvořeno).

Například konfigurace protokolování je běžně poskytována v `Logging` části soubory nastavení aplikace. Následující příklad ukazuje obsah typické *appsettings.Development.js* souboru:

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

`Logging`Vlastnost může mít `LogLevel` vlastnosti zprostředkovatele a log (zobrazí se konzola).

`LogLevel`Vlastnost v rámci `Logging` Určuje minimální [úroveň](#log-level) protokolu pro vybrané kategorie. V tomto příkladu se `System` `Microsoft` kategorie přihlásí na `Information` úrovni a všechny ostatní protokoly na `Debug` úrovni.

Další vlastnosti v části `Logging` zadat zprostředkovatele protokolování. Příklad je pro poskytovatele konzoly. Pokud zprostředkovatel podporuje [obory protokolů](#log-scopes), `IncludeScopes` označuje, zda jsou povoleny. Vlastnost poskytovatele (například `Console` v příkladu) může také určovat `LogLevel` vlastnost. `LogLevel`v rámci zprostředkovatele Určuje úrovně, které se mají protokolovat pro daného poskytovatele.

Pokud jsou úrovně specifikovány v `Logging.{providername}.LogLevel` , přepíše cokoli nastavené v `Logging.LogLevel` . Zvažte například následující kód JSON:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

V předchozím formátu JSON `Console` nastavení zprostředkovatele přepíše předchozí (výchozí) úroveň protokolu.

Rozhraní API pro protokolování neobsahuje scénář pro změnu úrovní protokolu, když je aplikace spuštěná. Někteří poskytovatelé konfigurace ale mohou znovu načíst konfiguraci, která se projeví okamžitě při konfiguraci protokolování. Například [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider), který je přidán nástrojem `CreateDefaultBuilder` ke čtení souborů nastavení, ve výchozím nastavení znovu načte konfiguraci protokolování. Pokud se konfigurace v kódu změní, když je aplikace spuštěná, může aplikace volat [IConfigurationRoot. Load](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby se aktualizovala konfigurace protokolování aplikace.

Informace o implementaci zprostředkovatelů konfigurace najdete v tématu <xref:fundamentals/configuration/index> .

## <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Pomocí ukázkového kódu zobrazeného v předchozí části se protokoly zobrazí v konzole nástroje při spuštění aplikace z příkazového řádku. Tady je příklad výstupu konzoly:

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

Předchozí protokoly byly vygenerovány vytvořením žádosti HTTP GET do ukázkové aplikace na adrese `http://localhost:5000/api/todo/0` .

Tady je příklad stejného protokolu, který se zobrazí v okně ladění při spuštění ukázkové aplikace v aplikaci Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Protokoly, které jsou vytvořené voláními, které jsou `ILogger` uvedené v předchozí části, začínají na "TodoApi". Protokoly, které začínají na kategorie Microsoft, jsou z ASP.NET Coreho kódu rozhraní. ASP.NET Core a kód aplikace používají stejné rozhraní API a poskytovatele protokolování.

Zbývající část tohoto článku vysvětluje několik podrobností a možností protokolování.

## <a name="nuget-packages"></a>Balíčky NuGet

`ILogger`Rozhraní a `ILoggerFactory` jsou v [Microsoft. Extensions. Loggings. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a výchozí implementace pro ně jsou v [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategorie protokolu

Při `ILogger` vytvoření objektu je pro něj zadána *kategorie* . Tato kategorie je součástí každé zprávy protokolu vytvořené pomocí této instance `ILogger` . Kategorie může být libovolný řetězec, ale konvence používá název třídy, jako je například "TodoApi. Controllers. TodoController".

Použijte `ILogger<T>` k získání `ILogger` instance, která používá plně kvalifikovaný název typu `T` jako kategorii:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Chcete-li explicitně zadat kategorii, zavolejte `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`je ekvivalentní volání `CreateLogger` s plně kvalifikovaným názvem typu `T` .

## <a name="log-level"></a>Úroveň protokolování

Každý protokol Určuje <xref:Microsoft.Extensions.Logging.LogLevel> hodnotu. Úroveň protokolu označuje závažnost nebo důležitost. Můžete například zapsat `Information` protokol, pokud metoda končí normálně a `Warning` protokol, když metoda vrátí stavový kód *404 Nenalezeno* .

Následující kód vytváří `Information` a `Warning` protokoluje:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

V předchozím kódu `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` parametry a jsou [ID události protokolu](#log-event-id). Druhý parametr je šablona zprávy se zástupnými symboly pro hodnoty argumentů poskytované zbývajícími parametry metody. Parametry metody jsou vysvětleny v [části Šablona zprávy protokolu](#lmt) v tomto článku.

Metody protokolu, které zahrnují úroveň v názvu metody (například `LogInformation` a `LogWarning` ), jsou [metody rozšíření pro ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Tyto metody volají `Log` metodu, která přebírá `LogLevel` parametr. Metodu můžete zavolat `Log` přímo místo jedné z těchto rozšiřujících metod, ale syntaxe je poměrně složitá. Další informace naleznete v tématu <xref:Microsoft.Extensions.Logging.ILogger> a ve [zdrojovém kódu rozšíření protokolovacího](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)nástroje.

ASP.NET Core definuje následující úrovně protokolu seřazené od nejnižší k nejvyšší závažnosti.

* Trasování = 0

  Informace, které jsou obvykle užitečné pouze pro ladění. Tyto zprávy mohou obsahovat citlivá data aplikací, a proto by neměly být povoleny v produkčním prostředí. *Ve výchozím nastavení zakázáno.*

* Ladění = 1

  Informace, které mohou být užitečné při vývoji a ladění. Příklad: `Entering method Configure with flag set to true.` Povolte `Debug` protokoly úrovně v produkčním prostředí jenom při řešení potíží, protože se jedná o velký objem protokolů.

* Informace = 2

  Pro sledování obecného toku aplikace. Tyto protokoly obvykle mají nějakou dlouhodobou hodnotu. Příklad: `Request received for path /api/todo`

* Upozornění = 3

  Pro neobvyklé nebo neočekávané události v toku aplikace. Ty můžou zahrnovat chyby nebo jiné podmínky, které nezpůsobí zastavení aplikace, ale možná bude nutné je prozkoumat. Zpracování výjimek je běžné místo pro použití `Warning` úrovně protokolu. Příklad: `FileNotFoundException for file quotes.txt.`

* Chyba = 4

  Pro chyby a výjimky, které nelze zpracovat. Tyto zprávy indikují selhání aktuální aktivity nebo operace (jako je aktuální požadavek HTTP), ne při selhání celé aplikace. Příklad zprávy protokolu:`Cannot insert record due to duplicate key violation.`

* Kritické = 5

  Chyby, které vyžadují okamžitou pozornost. Příklady: scénáře ztráty dat, nedostatek místa na disku.

Úroveň protokolu můžete použít k určení, kolik výstupu protokolu se zapisuje do konkrétního úložného média nebo okna pro zobrazení. Příklad:

* V produkčním prostředí:
  * Protokolování na úrovni `Trace` přes `Information` vytváří velké množství podrobných zpráv protokolu. Pokud chcete řídit náklady a nepřekročit limity úložiště dat, protokolujte `Trace` `Information` zprávy na úrovni do úložiště dat s nízkými náklady na velké objemy dat.
  * Přihlášení `Warning` přes `Critical` úrovně obvykle vytváří méně a menší zprávy protokolu. Proto náklady a limity úložiště většinou nejsou obavy, což má za následek větší flexibilitu výběru úložiště dat.
* Během vývoje:
  * Umožňuje protokolovat `Warning` `Critical` zprávy do konzoly.
  * `Trace` `Information` Při odstraňování potíží přidejte zprávy.

Část [filtrování protokolu](#log-filtering) dále v tomto článku vysvětluje, jak řídit, které úrovně protokolu zprostředkovateli zpracovává.

ASP.NET Core zapisuje protokoly pro události rozhraní .NET Framework. Příklady protokolů výše v tomto článku vyloučily nižší `Information` úrovně protokolů, takže se `Debug` `Trace` nevytvořily žádné protokoly ani protokoly na úrovni. Tady je příklad protokolů konzoly vytvořených spuštěním ukázkové aplikace nakonfigurované pro zobrazení `Debug` protokolů:

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

Každý protokol může určovat *ID události*. Tato ukázková aplikace používá lokálně definovanou `LoggingEvents` třídu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

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

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

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

Pokud odesíláte protokoly do služby Azure Table Storage, Každá entita tabulky Azure může mít `ID` vlastnosti a `RequestTime` , což zjednodušuje dotazy na data protokolu. Dotaz může najít všechny protokoly v rámci určitého `RequestTime` rozsahu, aniž by došlo k analýze časového limitu textové zprávy.

## <a name="logging-exceptions"></a>Protokolování výjimek

Metody protokolovacího nástroje mají přetížení, které umožňují předat výjimku, jak je uvedeno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

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

Kód šablony projektu volá `CreateDefaultBuilder` k nastavení protokolování pro poskytovatele, konzolu, ladění a EventSource (ASP.NET Core 2,2 nebo novější). `CreateDefaultBuilder`Metoda nastaví protokolování tak, aby hledalo konfiguraci v `Logging` části, jak je vysvětleno [výše v tomto článku](#configuration).

Konfigurační data určují minimální úrovně protokolu podle poskytovatele a kategorie, jako v následujícím příkladu:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Tento kód JSON vytvoří šest pravidel filtru: jeden pro poskytovatele ladění, čtyři pro poskytovatele konzoly a jeden pro všechny poskytovatele. Při vytvoření objektu se vybere jedno pravidlo pro každého poskytovatele `ILogger` .

### <a name="filter-rules-in-code"></a>Filtrovat pravidla v kódu

Následující příklad ukazuje, jak registrovat pravidla filtru v kódu:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Druhý `AddFilter` Určuje poskytovatele ladění pomocí jeho názvu typu. První `AddFilter` se vztahuje na všechny poskytovatele, protože neurčuje typ poskytovatele.

### <a name="how-filtering-rules-are-applied"></a>Jak se používají pravidla filtrování

Konfigurační data a `AddFilter` kód zobrazený v předchozích příkladech vytvoří pravidla uvedená v následující tabulce. Prvních šest přicházejí z příkladu konfigurace a poslední dva pocházejí z příkladu kódu.

| Číslo | Poskytovatel      | Kategorie, které začínají na...          | Minimální úroveň protokolování |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Ladění         | Všechny kategorie                          | Informace       |
| 2      | Konzola       | Microsoft. AspNetCore. Mvc. Razor .. Vnitřních | Upozornění           |
| 3      | Konzola       | Microsoft. AspNetCore. Mvc. Razor ..Razor    | Ladění             |
| 4      | Konzola       | Microsoft. AspNetCore. Mvc.Razor          | Chyba             |
| 5      | Konzola       | Všechny kategorie                          | Informace       |
| 6      | Všichni poskytovatelé | Všechny kategorie                          | Ladění             |
| 7      | Všichni poskytovatelé | Systém                                  | Ladění             |
| 8      | Ladění         | Partnerský vztah Microsoftu                               | Trasování             |

Při `ILogger` vytvoření objektu `ILoggerFactory` vybere objekt jedno pravidlo pro každého poskytovatele, které se použije pro tento protokolovací nástroj. Všechny zprávy napsané `ILogger` instancí jsou filtrovány podle vybraných pravidel. V dostupných pravidlech se vybere nejpřesnější pravidlo pro jednotlivé dvojice zprostředkovatel a kategorie.

Následující algoritmus se používá pro každého poskytovatele při `ILogger` vytvoření pro danou kategorii:

* Vyberte všechna pravidla, která se shodují se zprostředkovatelem nebo jeho aliasem. Pokud se nenajde žádná shoda, vyberte všechna pravidla s prázdným zprostředkovatelem.
* Z výsledku předchozího kroku vyberte pravidla s nejdelší vyhovující předponou kategorie. Pokud se nenajde žádná shoda, vyberte všechna pravidla, která neurčují kategorii.
* Pokud je vybráno více pravidel, vezměte **Poslední** z nich.
* Pokud nejsou vybrána žádná pravidla, použijte `MinimumLevel` .

V předchozím seznamu pravidel Předpokládejme, že vytvoříte `ILogger` objekt pro kategorii "Microsoft. AspNetCore. Mvc. Razor . Razor ViewEngine":

* Pro poskytovatele ladění platí pravidla 1, 6 a 8. Pravidlo 8 je nejvíce specifické, takže je to ten vybraný.
* Pro poskytovatele konzoly platí pravidla 3, 4, 5 a 6. Pravidlo 3 je nejvíce specifické.

Výsledná `ILogger` instance odesílá protokoly `Trace` úrovně a výše do poskytovatele ladění. Protokoly `Debug` úrovně a výše jsou odesílány poskytovateli konzoly.

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

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Pokud nenastavíte explicitně minimální úroveň, výchozí hodnota je `Information` , což znamená, že `Trace` protokoly a `Debug` budou ignorovány.

### <a name="filter-functions"></a>Funkce filtrování

Funkce filtru je vyvolána pro všechny poskytovatele a kategorie, které nemají pravidla přiřazena pomocí konfigurace nebo kódu. Kód ve funkci má přístup k typu poskytovatele, kategorii a úrovni protokolu. Příklad:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Systémové kategorie a úrovně

Tady je několik kategorií používaných ASP.NET Core a Entity Framework Core s poznámkami o tom, jaké protokoly se mají od nich očekávat:

| Kategorie                            | Poznámky |
| ----------------------------------- | ----- |
| Microsoft. AspNetCore                | Obecná diagnostika ASP.NET Core. |
| Microsoft. AspNetCore. DataProtection | Které klíče byly zváženy, nalezeny a použity. |
| Microsoft. AspNetCore. HostFiltering  | Hostitelé povoleni. |
| Microsoft. AspNetCore. hosting        | Doba, po kterou trvalo dokončení požadavků HTTP a čas jejich spuštění. Která hostující spouštěcí sestavení byla načtena. |
| Microsoft. AspNetCore. Mvc            | MVC a Razor Diagnostika. Vazba modelů, spuštění filtru, zobrazení kompilace, výběr akce. |
| Microsoft. AspNetCore. Routing        | Informace o shodě trasy. |
| Microsoft. AspNetCore. Server         | Připojení – spouštění, zastavování a udržování reakcí na Alive. Informace o certifikátu HTTPS |
| Microsoft. AspNetCore. StaticFiles    | Soubory byly obsluhovány. |
| Microsoft. EntityFrameworkCore       | Obecná diagnostika Entity Framework Core. Databázová aktivita a konfigurace, detekce změn, migrace. |

## <a name="log-scopes"></a>Rozsahy protokolů

 *Obor* může seskupit sadu logických operací. Toto seskupení lze použít pro připojení stejných dat ke každému protokolu, který je vytvořen jako součást sady. Každý protokol vytvořený jako součást zpracování transakce může například zahrnovat ID transakce.

Obor je `IDisposable` typ, který je vrácen <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodou a trvá, dokud nebude uvolněn. Použití oboru zabalením volání protokolovacího nástroje v `using` bloku:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Následující kód umožňuje obory pro poskytovatele konzoly:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> `IncludeScopes`Aby bylo možné povolit protokolování na základě oboru, je nutné nakonfigurovat možnost protokolovacího nástroje konzoly.
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

* [Konzola](#console-provider)
* [Ladění](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Informace o stdout a protokolování ladění pomocí modulu ASP.NET Core naleznete v tématu <xref:test/troubleshoot-azure-iis> a <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

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

Balíček poskytovatele [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje výstup protokolu pomocí třídy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) ( `Debug.WriteLine` volání metody).

V systému Linux tento poskytovatel zapisuje protokoly do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Zprostředkovatel zdroje událostí

Balíček zprostředkovatele [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje do zdroje událostí pro různé platformy s názvem `Microsoft-Extensions-Logging` . Ve Windows zprostředkovatel používá [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Poskytovatel zdroje událostí se přidá automaticky, když `CreateDefaultBuilder` se volá k sestavení hostitele.

K shromažďování a zobrazování protokolů použijte [Nástroj PerfView](https://github.com/Microsoft/perfview) . Existují i další nástroje pro zobrazení protokolů ETW, ale PerfView poskytuje nejlepší prostředí pro práci s událostmi trasování událostí pro Windows vygenerovanými ASP.NET Core.

Pokud chcete nakonfigurovat PerfView pro shromažďování událostí protokolovaných tímto poskytovatelem, přidejte řetězec `*Microsoft-Extensions-Logging` do seznamu **další poskytovatelé** . (Na začátku řetězce nechybíš hvězdičku.)

![PerfView další poskytovatelé](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Zprostředkovatel událostí systému Windows

Balíček poskytovatele [Microsoft. Extensions. log. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) odesílá výstup protokolu do protokolu událostí systému Windows.

```csharp
logging.AddEventLog();
```

[AddEventLog přetížení](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umožňují předat <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Pokud `null` není zadaný, použijí se následující výchozí nastavení:

* `LogName`: "Application"
* `SourceName`: ".NET runtime"
* `MachineName`: Používá se název místního počítače.

Události jsou protokolovány pro [úroveň upozornění a vyšší](#log-level). Následující příklad nastaví výchozí úroveň protokolu událostí na <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>Poskytovatel TraceSource

Balíček poskytovatele [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) používá <xref:System.Diagnostics.TraceSource> knihovny a poskytovatele.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Přetížení AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umožňují předat zdrojový přepínač a naslouchací proces trasování.

Aby bylo možné používat tohoto poskytovatele, aplikace musí běžet na .NET Framework (spíše než .NET Core). Poskytovatel může směrovat zprávy na celou řadu [posluchačů](/dotnet/framework/debug-trace-profile/trace-listeners), jako je například <xref:System.Diagnostics.TextWriterTraceListener> použití v ukázkové aplikaci.

### <a name="azure-app-service-provider"></a>Poskytovatel Azure App Service

Balíček poskytovatele [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje protokoly do textových souborů v systému souborů aplikace Azure App Service a do [úložiště objektů BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) v Azure Storagem účtu.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Balíček poskytovatele není zahrnutý ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app). Při cílení na .NET Framework nebo odkazování na `Microsoft.AspNetCore.App` Metapackage přidejte do projektu balíček poskytovatele. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Přetížení umožňuje předat <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> . Objekt nastavení může přepsat výchozí nastavení, jako je například šablona výstupu protokolování, název objektu BLOB a omezení velikosti souboru. (*Výstupní šablona* je šablona zprávy, která se používá pro všechny protokoly kromě toho, co je k dispozici při `ILogger` volání metody.)

Když nasadíte aplikaci do App Service, aplikace respektuje nastavení v části [protokoly App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) stránky **App Service** Azure Portal. Když se aktualizují následující nastavení, změny se projeví okamžitě bez nutnosti restartování nebo opětovného nasazení aplikace.

* **Protokolování aplikace (systém souborů)**
* **Protokolování aplikace (BLOB)**

Výchozí umístění souborů protokolu je ve složce *D: Home program \\ \\ Logfiles \\ * a výchozí název souboru je *diagnostics-yyyymmdd.txt*. Výchozí omezení velikosti souboru je 10 MB a výchozí maximální počet uchovávaných souborů je 2. Výchozí název objektu BLOB je *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.

Zprostředkovatel funguje pouze v případě, že projekt běží v prostředí Azure. Nemá žádný vliv, pokud se projekt spouští místně &mdash; , nepíše do místních souborů nebo místního vývojového úložiště pro objekty blob.

#### <a name="azure-log-streaming"></a>Streamování protokolů Azure

Streamování protokolů Azure umožňuje zobrazit aktivitu protokolu v reálném čase z:

* Aplikační server
* Webový server
* Trasování chybných požadavků

Konfigurace streamování protokolů Azure:

* Na stránce portálu vaší aplikace přejděte na stránku **protokoly App Service** .
* Nastavte **protokolování aplikace (systém souborů)** na **zapnuto**.
* Vyberte **úroveň**protokolu. Toto nastavení platí jenom pro streamování protokolů Azure, ne pro jiné poskytovatele protokolování v aplikaci.

Pokud chcete zobrazit zprávy aplikace, přejděte na stránku **streamu protokolu** . Protokolují ho aplikace přes `ILogger` rozhraní.

### <a name="azure-application-insights-trace-logging"></a>Protokolování trasování Azure Application Insights

Balíček poskytovatele [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje protokoly do Azure Application Insights. Application Insights je služba, která monitoruje webovou aplikaci a poskytuje nástroje pro dotazování a analýzu dat telemetrie. Pokud použijete tohoto poskytovatele, můžete zadávat dotazy a analyzovat protokoly pomocí Application Insightsch nástrojů.

Zprostředkovatel protokolování je zahrnutý jako závislost na [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), což je balíček, který poskytuje veškerou dostupnou telemetrii pro ASP.NET Core. Pokud použijete tento balíček, nemusíte instalovat balíček poskytovatele.

Nepoužívejte balíček [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash; , který je pro ASP.NET 4. x.

Další informace naleznete v následujících zdrojích:

* [Přehled služby Application Insights](/azure/application-insights/app-insights-overview)
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
1. Zavolejte `ILoggerFactory` metodu rozšíření poskytovanou protokolovacím rozhraním.

Další informace najdete v dokumentaci pro každého poskytovatele. Microsoft nepodporuje zprostředkovatele protokolování třetích stran.

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
