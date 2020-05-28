---
<span data-ttu-id="6b531-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6b531-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6b531-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6b531-102">'Blazor'</span></span>
- <span data-ttu-id="6b531-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6b531-103">'Identity'</span></span>
- <span data-ttu-id="6b531-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6b531-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6b531-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6b531-105">'Razor'</span></span>
- <span data-ttu-id="6b531-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="6b531-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="6b531-107">Protokolování s vysokým výkonem pomocí LoggerMessage v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b531-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6b531-108"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkce vytvářejí delegáty, které umožňují ukládání do mezipaměti, které vyžadují méně přidělení objektů a snižují výpočetní režii v porovnání s [metodami rozšíření protokolovacího](xref:Microsoft.Extensions.Logging.LoggerExtensions)nástroje, jako jsou <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="6b531-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="6b531-109">Pro scénáře protokolování s vysokým výkonem použijte <xref:Microsoft.Extensions.Logging.LoggerMessage> vzor.</span><span class="sxs-lookup"><span data-stu-id="6b531-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="6b531-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu v rámci rozšiřujících metod protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="6b531-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="6b531-111">Metody rozšíření protokolovacího nástroje vyžadují "zabalení" (převod) typů hodnot, například `int` do `object` .</span><span class="sxs-lookup"><span data-stu-id="6b531-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="6b531-112"><xref:Microsoft.Extensions.Logging.LoggerMessage>Vzor zabraňuje zabalení pomocí statických <xref:System.Action> polí a metod rozšíření s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="6b531-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="6b531-113">Metody rozšíření protokolovacího nástroje musí při každém zápisu zprávy protokolu analyzovat šablonu zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="6b531-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="6b531-114"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze při definování zprávy vyžaduje pouze analýzu šablony.</span><span class="sxs-lookup"><span data-stu-id="6b531-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="6b531-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6b531-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6b531-116">Ukázková aplikace ukazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkce se základním systémem pro sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="6b531-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="6b531-117">Aplikace přidá a odstraní uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="6b531-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="6b531-118">Při výskytu těchto operací se zprávy protokolu generují pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage> vzoru.</span><span class="sxs-lookup"><span data-stu-id="6b531-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="6b531-119">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="6b531-119">LoggerMessage.Define</span></span>

<span data-ttu-id="6b531-120">[Define (LogLevel, ID události, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří <xref:System.Action> delegáta pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="6b531-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="6b531-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení povolují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="6b531-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="6b531-122">Řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="6b531-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="6b531-123">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="6b531-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="6b531-124">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="6b531-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="6b531-125">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="6b531-126">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="6b531-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="6b531-127">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="6b531-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="6b531-128">Každá zpráva protokolu je <xref:System.Action> držená ve statickém poli vytvořeném pomocí [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="6b531-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="6b531-129">Ukázková aplikace například vytvoří pole pro popis zprávy protokolu pro požadavek GET na stránku indexu (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="6b531-130">Pro <xref:System.Action> zadejte:</span><span class="sxs-lookup"><span data-stu-id="6b531-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="6b531-131">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="6b531-131">The log level.</span></span>
* <span data-ttu-id="6b531-132">Jedinečný identifikátor události ( <xref:Microsoft.Extensions.Logging.EventId> ) s názvem statické metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6b531-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="6b531-133">Šablona zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="6b531-133">The message template (named format string).</span></span> 

<span data-ttu-id="6b531-134">Požadavek na stránku index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="6b531-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="6b531-135">Úroveň protokolu do `Information` .</span><span class="sxs-lookup"><span data-stu-id="6b531-135">Log level to `Information`.</span></span>
* <span data-ttu-id="6b531-136">ID události na `1` název `IndexPageRequested` metody</span><span class="sxs-lookup"><span data-stu-id="6b531-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="6b531-137">Šablona zprávy (pojmenovaný řetězec formátu) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="6b531-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="6b531-138">Úložiště strukturovaného protokolování můžou použít název události, když se poskytne s ID události pro rozšíření protokolování.</span><span class="sxs-lookup"><span data-stu-id="6b531-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="6b531-139">[Serilog](https://github.com/serilog/serilog-extensions-logging) například používá název události.</span><span class="sxs-lookup"><span data-stu-id="6b531-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="6b531-140"><xref:System.Action>Je vyvolána prostřednictvím rozšiřující metody silného typu.</span><span class="sxs-lookup"><span data-stu-id="6b531-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="6b531-141">`IndexPageRequested`Metoda zaznamená zprávu pro požadavek na stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6b531-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="6b531-142">`IndexPageRequested`se volá v protokolovacím nástroji v `OnGetAsync` metodě na *stránkách pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="6b531-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="6b531-143">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="6b531-144">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="6b531-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="6b531-145">Ukázková aplikace při přidávání nabídky zaznamená řetězec tak, že definuje `string` typ <xref:System.Action> pole:</span><span class="sxs-lookup"><span data-stu-id="6b531-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="6b531-146">Šablona zprávy protokolu delegáta přijímá své zástupné hodnoty z poskytnutých typů.</span><span class="sxs-lookup"><span data-stu-id="6b531-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="6b531-147">Ukázková aplikace definuje delegáta pro přidání nabídky, kde je parametrem nabídky `string` :</span><span class="sxs-lookup"><span data-stu-id="6b531-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="6b531-148">Statická rozšiřující metoda pro přidání nabídky, `QuoteAdded` přijímá hodnotu argumentu citace a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="6b531-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="6b531-149">V modelu stránky indexu stránky (*pages/index. cshtml. cs*) `QuoteAdded` se volá, aby se zaprotokoloval zpráva:</span><span class="sxs-lookup"><span data-stu-id="6b531-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="6b531-150">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="6b531-151">Ukázková aplikace implementuje vzor [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="6b531-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="6b531-152">Informační zpráva se zaznamená do protokolu pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="6b531-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="6b531-153">Pokud je vyvolána výjimka, je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="6b531-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="6b531-154">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimky (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="6b531-155">Všimněte si, jak je výjimka předána delegátovi v `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="6b531-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="6b531-156">V modelu stránky pro stránku index je úspěšné odstranění citace voláním `QuoteDeleted` metody protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="6b531-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="6b531-157">Pokud se nabídka nenajde pro odstranění, <xref:System.ArgumentNullException> vyvolá se.</span><span class="sxs-lookup"><span data-stu-id="6b531-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="6b531-158">Výjimka je zachycena příkazem [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) a protokolována voláním metody v `QuoteDeleteFailed` protokolovacím nástroji v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="6b531-159">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="6b531-160">Pokud se odstranění citace nepovede, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b531-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="6b531-161">Všimněte si, že je tato výjimka součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="6b531-161">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="6b531-162">LoggerMessage. Definescope –</span><span class="sxs-lookup"><span data-stu-id="6b531-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="6b531-163">[Definescope – (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="6b531-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="6b531-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení povolují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="6b531-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="6b531-165">Stejně jako v případě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="6b531-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="6b531-166">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="6b531-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="6b531-167">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="6b531-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="6b531-168">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="6b531-169">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="6b531-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="6b531-170">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="6b531-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="6b531-171">Definujte [Rozsah protokolu](xref:fundamentals/logging/index#log-scopes) , který se má použít pro řadu zpráv protokolu pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="6b531-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="6b531-172">Ukázková aplikace má tlačítko **Zrušit vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="6b531-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="6b531-173">Tyto nabídky jsou odstraněny po jednom jejich odebráním.</span><span class="sxs-lookup"><span data-stu-id="6b531-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="6b531-174">Pokaždé, když se odstraní citace, `QuoteDeleted` metoda se zavolá do protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="6b531-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="6b531-175">Do těchto zpráv protokolu se přidá rozsah protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="6b531-176">Povolit `IncludeScopes` v části protokolovacího nástroje konzoly souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b531-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="6b531-177">Chcete-li vytvořit rozsah protokolu, přidejte pole pro blokování <xref:System.Func%601> delegáta oboru.</span><span class="sxs-lookup"><span data-stu-id="6b531-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="6b531-178">Ukázková aplikace vytvoří pole s názvem `_allQuotesDeletedScope` (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="6b531-179">Použijte <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="6b531-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="6b531-180">Až tři typy lze zadat pro použití jako argumenty šablony při volání delegáta.</span><span class="sxs-lookup"><span data-stu-id="6b531-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="6b531-181">Ukázková aplikace používá šablonu zprávy, která obsahuje počet odstraněných uvozovek ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="6b531-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="6b531-182">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="6b531-183">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="6b531-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="6b531-184">Ukázková aplikace se převezme v `count` uvozovkách, které se mají odstranit a vrátí `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="6b531-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="6b531-185">Obor zabalí volání rozšíření protokolování v bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="6b531-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="6b531-186">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b531-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="6b531-187">Následující výsledek obsahuje tři nabídky odstraněné se zprávou rozsah protokolu, která je součástí:</span><span class="sxs-lookup"><span data-stu-id="6b531-187">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b531-188"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkce vytvářejí delegáty, které umožňují ukládání do mezipaměti, které vyžadují méně přidělení objektů a snižují výpočetní režii v porovnání s [metodami rozšíření protokolovacího](xref:Microsoft.Extensions.Logging.LoggerExtensions)nástroje, jako jsou <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="6b531-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="6b531-189">Pro scénáře protokolování s vysokým výkonem použijte <xref:Microsoft.Extensions.Logging.LoggerMessage> vzor.</span><span class="sxs-lookup"><span data-stu-id="6b531-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="6b531-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu v rámci rozšiřujících metod protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="6b531-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="6b531-191">Metody rozšíření protokolovacího nástroje vyžadují "zabalení" (převod) typů hodnot, například `int` do `object` .</span><span class="sxs-lookup"><span data-stu-id="6b531-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="6b531-192"><xref:Microsoft.Extensions.Logging.LoggerMessage>Vzor zabraňuje zabalení pomocí statických <xref:System.Action> polí a metod rozšíření s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="6b531-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="6b531-193">Metody rozšíření protokolovacího nástroje musí při každém zápisu zprávy protokolu analyzovat šablonu zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="6b531-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="6b531-194"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze při definování zprávy vyžaduje pouze analýzu šablony.</span><span class="sxs-lookup"><span data-stu-id="6b531-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="6b531-195">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6b531-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6b531-196">Ukázková aplikace ukazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkce se základním systémem pro sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="6b531-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="6b531-197">Aplikace přidá a odstraní uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="6b531-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="6b531-198">Při výskytu těchto operací se zprávy protokolu generují pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage> vzoru.</span><span class="sxs-lookup"><span data-stu-id="6b531-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="6b531-199">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="6b531-199">LoggerMessage.Define</span></span>

<span data-ttu-id="6b531-200">[Define (LogLevel, ID události, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří <xref:System.Action> delegáta pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="6b531-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="6b531-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení povolují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="6b531-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="6b531-202">Řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="6b531-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="6b531-203">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="6b531-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="6b531-204">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="6b531-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="6b531-205">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="6b531-206">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="6b531-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="6b531-207">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="6b531-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="6b531-208">Každá zpráva protokolu je <xref:System.Action> držená ve statickém poli vytvořeném pomocí [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="6b531-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="6b531-209">Ukázková aplikace například vytvoří pole pro popis zprávy protokolu pro požadavek GET na stránku indexu (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="6b531-210">Pro <xref:System.Action> zadejte:</span><span class="sxs-lookup"><span data-stu-id="6b531-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="6b531-211">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="6b531-211">The log level.</span></span>
* <span data-ttu-id="6b531-212">Jedinečný identifikátor události ( <xref:Microsoft.Extensions.Logging.EventId> ) s názvem statické metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="6b531-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="6b531-213">Šablona zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="6b531-213">The message template (named format string).</span></span> 

<span data-ttu-id="6b531-214">Požadavek na stránku index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="6b531-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="6b531-215">Úroveň protokolu do `Information` .</span><span class="sxs-lookup"><span data-stu-id="6b531-215">Log level to `Information`.</span></span>
* <span data-ttu-id="6b531-216">ID události na `1` název `IndexPageRequested` metody</span><span class="sxs-lookup"><span data-stu-id="6b531-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="6b531-217">Šablona zprávy (pojmenovaný řetězec formátu) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="6b531-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="6b531-218">Úložiště strukturovaného protokolování můžou použít název události, když se poskytne s ID události pro rozšíření protokolování.</span><span class="sxs-lookup"><span data-stu-id="6b531-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="6b531-219">[Serilog](https://github.com/serilog/serilog-extensions-logging) například používá název události.</span><span class="sxs-lookup"><span data-stu-id="6b531-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="6b531-220"><xref:System.Action>Je vyvolána prostřednictvím rozšiřující metody silného typu.</span><span class="sxs-lookup"><span data-stu-id="6b531-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="6b531-221">`IndexPageRequested`Metoda zaznamená zprávu pro požadavek na stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6b531-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="6b531-222">`IndexPageRequested`se volá v protokolovacím nástroji v `OnGetAsync` metodě na *stránkách pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="6b531-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="6b531-223">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="6b531-224">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="6b531-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="6b531-225">Ukázková aplikace při přidávání nabídky zaznamená řetězec tak, že definuje `string` typ <xref:System.Action> pole:</span><span class="sxs-lookup"><span data-stu-id="6b531-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="6b531-226">Šablona zprávy protokolu delegáta přijímá své zástupné hodnoty z poskytnutých typů.</span><span class="sxs-lookup"><span data-stu-id="6b531-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="6b531-227">Ukázková aplikace definuje delegáta pro přidání nabídky, kde je parametrem nabídky `string` :</span><span class="sxs-lookup"><span data-stu-id="6b531-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="6b531-228">Statická rozšiřující metoda pro přidání nabídky, `QuoteAdded` přijímá hodnotu argumentu citace a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="6b531-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="6b531-229">V modelu stránky indexu stránky (*pages/index. cshtml. cs*) `QuoteAdded` se volá, aby se zaprotokoloval zpráva:</span><span class="sxs-lookup"><span data-stu-id="6b531-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="6b531-230">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="6b531-231">Ukázková aplikace implementuje vzor [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="6b531-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="6b531-232">Informační zpráva se zaznamená do protokolu pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="6b531-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="6b531-233">Pokud je vyvolána výjimka, je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="6b531-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="6b531-234">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimky (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="6b531-235">Všimněte si, jak je výjimka předána delegátovi v `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="6b531-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="6b531-236">V modelu stránky pro stránku index je úspěšné odstranění citace voláním `QuoteDeleted` metody protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="6b531-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="6b531-237">Pokud se nabídka nenajde pro odstranění, <xref:System.ArgumentNullException> vyvolá se.</span><span class="sxs-lookup"><span data-stu-id="6b531-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="6b531-238">Výjimka je zachycena příkazem [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) a protokolována voláním metody v `QuoteDeleteFailed` protokolovacím nástroji v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="6b531-239">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="6b531-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="6b531-240">Pokud se odstranění citace nepovede, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b531-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="6b531-241">Všimněte si, že je tato výjimka součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="6b531-241">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="6b531-242">LoggerMessage. Definescope –</span><span class="sxs-lookup"><span data-stu-id="6b531-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="6b531-243">[Definescope – (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="6b531-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="6b531-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení povolují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="6b531-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="6b531-245">Stejně jako v případě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="6b531-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="6b531-246">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="6b531-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="6b531-247">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="6b531-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="6b531-248">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="6b531-249">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="6b531-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="6b531-250">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="6b531-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="6b531-251">Definujte [Rozsah protokolu](xref:fundamentals/logging/index#log-scopes) , který se má použít pro řadu zpráv protokolu pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="6b531-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="6b531-252">Ukázková aplikace má tlačítko **Zrušit vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="6b531-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="6b531-253">Tyto nabídky jsou odstraněny po jednom jejich odebráním.</span><span class="sxs-lookup"><span data-stu-id="6b531-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="6b531-254">Pokaždé, když se odstraní citace, `QuoteDeleted` metoda se zavolá do protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="6b531-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="6b531-255">Do těchto zpráv protokolu se přidá rozsah protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="6b531-256">Povolit `IncludeScopes` v části protokolovacího nástroje konzoly souboru *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6b531-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="6b531-257">Chcete-li vytvořit rozsah protokolu, přidejte pole pro blokování <xref:System.Func%601> delegáta oboru.</span><span class="sxs-lookup"><span data-stu-id="6b531-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="6b531-258">Ukázková aplikace vytvoří pole s názvem `_allQuotesDeletedScope` (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="6b531-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="6b531-259">Použijte <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="6b531-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="6b531-260">Až tři typy lze zadat pro použití jako argumenty šablony při volání delegáta.</span><span class="sxs-lookup"><span data-stu-id="6b531-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="6b531-261">Ukázková aplikace používá šablonu zprávy, která obsahuje počet odstraněných uvozovek ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="6b531-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="6b531-262">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="6b531-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="6b531-263">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="6b531-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="6b531-264">Ukázková aplikace se převezme v `count` uvozovkách, které se mají odstranit a vrátí `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="6b531-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="6b531-265">Obor zabalí volání rozšíření protokolování v bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="6b531-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="6b531-266">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b531-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="6b531-267">Následující výsledek obsahuje tři nabídky odstraněné se zprávou rozsah protokolu, která je součástí:</span><span class="sxs-lookup"><span data-stu-id="6b531-267">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6b531-268">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6b531-268">Additional resources</span></span>

* [<span data-ttu-id="6b531-269">Protokolování</span><span class="sxs-lookup"><span data-stu-id="6b531-269">Logging</span></span>](xref:fundamentals/logging/index)
