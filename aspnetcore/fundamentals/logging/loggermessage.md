---
title: Protokolování s vysokým výkonem pomocí LoggerMessage v ASP.NET Core
author: rick-anderson
description: Naučte se používat LoggerMessage k vytváření protokolovaných delegátů, kteří vyžadují méně přidělení objektů pro scénáře protokolování s vysokým výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: dc90c6cdbcea7c36079bcb4a032014998056b56d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401165"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="0724d-103">Protokolování s vysokým výkonem pomocí LoggerMessage v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0724d-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0724d-104"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkce vytvářejí delegáty, které umožňují ukládání do mezipaměti, které vyžadují méně přidělení objektů a snižují výpočetní režii v porovnání s [metodami rozšíření protokolovacího](xref:Microsoft.Extensions.Logging.LoggerExtensions)nástroje, jako jsou <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="0724d-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="0724d-105">Pro scénáře protokolování s vysokým výkonem použijte <xref:Microsoft.Extensions.Logging.LoggerMessage> vzor.</span><span class="sxs-lookup"><span data-stu-id="0724d-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="0724d-106"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu v rámci rozšiřujících metod protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="0724d-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="0724d-107">Metody rozšíření protokolovacího nástroje vyžadují "zabalení" (převod) typů hodnot, například `int` do `object` .</span><span class="sxs-lookup"><span data-stu-id="0724d-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="0724d-108"><xref:Microsoft.Extensions.Logging.LoggerMessage>Vzor zabraňuje zabalení pomocí statických <xref:System.Action> polí a metod rozšíření s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="0724d-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="0724d-109">Metody rozšíření protokolovacího nástroje musí při každém zápisu zprávy protokolu analyzovat šablonu zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="0724d-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="0724d-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze při definování zprávy vyžaduje pouze analýzu šablony.</span><span class="sxs-lookup"><span data-stu-id="0724d-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="0724d-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0724d-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0724d-112">Ukázková aplikace ukazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkce se základním systémem pro sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="0724d-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="0724d-113">Aplikace přidá a odstraní uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="0724d-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="0724d-114">Při výskytu těchto operací se zprávy protokolu generují pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage> vzoru.</span><span class="sxs-lookup"><span data-stu-id="0724d-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="0724d-115">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="0724d-115">LoggerMessage.Define</span></span>

<span data-ttu-id="0724d-116">[Define (LogLevel, ID události, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří <xref:System.Action> delegáta pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="0724d-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="0724d-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení povolují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="0724d-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="0724d-118">Řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="0724d-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0724d-119">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="0724d-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0724d-120">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="0724d-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0724d-121">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="0724d-122">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="0724d-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0724d-123">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0724d-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0724d-124">Každá zpráva protokolu je <xref:System.Action> držená ve statickém poli vytvořeném pomocí [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="0724d-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="0724d-125">Ukázková aplikace například vytvoří pole pro popis zprávy protokolu pro požadavek GET na stránku indexu (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="0724d-126">Pro <xref:System.Action> zadejte:</span><span class="sxs-lookup"><span data-stu-id="0724d-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="0724d-127">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="0724d-127">The log level.</span></span>
* <span data-ttu-id="0724d-128">Jedinečný identifikátor události ( <xref:Microsoft.Extensions.Logging.EventId> ) s názvem statické metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0724d-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="0724d-129">Šablona zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="0724d-129">The message template (named format string).</span></span> 

<span data-ttu-id="0724d-130">Požadavek na stránku index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="0724d-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="0724d-131">Úroveň protokolu do `Information` .</span><span class="sxs-lookup"><span data-stu-id="0724d-131">Log level to `Information`.</span></span>
* <span data-ttu-id="0724d-132">ID události na `1` název `IndexPageRequested` metody</span><span class="sxs-lookup"><span data-stu-id="0724d-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="0724d-133">Šablona zprávy (pojmenovaný řetězec formátu) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="0724d-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="0724d-134">Úložiště strukturovaného protokolování můžou použít název události, když se poskytne s ID události pro rozšíření protokolování.</span><span class="sxs-lookup"><span data-stu-id="0724d-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="0724d-135">[Serilog](https://github.com/serilog/serilog-extensions-logging) například používá název události.</span><span class="sxs-lookup"><span data-stu-id="0724d-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="0724d-136"><xref:System.Action>Je vyvolána prostřednictvím rozšiřující metody silného typu.</span><span class="sxs-lookup"><span data-stu-id="0724d-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="0724d-137">`IndexPageRequested`Metoda zaznamená zprávu pro požadavek na stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0724d-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="0724d-138">`IndexPageRequested`se volá v protokolovacím nástroji v `OnGetAsync` metodě na *stránkách pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0724d-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="0724d-139">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="0724d-140">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="0724d-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="0724d-141">Ukázková aplikace při přidávání nabídky zaznamená řetězec tak, že definuje `string` typ <xref:System.Action> pole:</span><span class="sxs-lookup"><span data-stu-id="0724d-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="0724d-142">Šablona zprávy protokolu delegáta přijímá své zástupné hodnoty z poskytnutých typů.</span><span class="sxs-lookup"><span data-stu-id="0724d-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="0724d-143">Ukázková aplikace definuje delegáta pro přidání nabídky, kde je parametrem nabídky `string` :</span><span class="sxs-lookup"><span data-stu-id="0724d-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="0724d-144">Statická rozšiřující metoda pro přidání nabídky, `QuoteAdded` přijímá hodnotu argumentu citace a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="0724d-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="0724d-145">V modelu stránky indexu stránky (*pages/index. cshtml. cs*) `QuoteAdded` se volá, aby se zaprotokoloval zpráva:</span><span class="sxs-lookup"><span data-stu-id="0724d-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="0724d-146">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="0724d-147">Ukázková aplikace implementuje vzor [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="0724d-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="0724d-148">Informační zpráva se zaznamená do protokolu pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="0724d-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="0724d-149">Pokud je vyvolána výjimka, je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="0724d-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="0724d-150">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimky (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="0724d-151">Všimněte si, jak je výjimka předána delegátovi v `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="0724d-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="0724d-152">V modelu stránky pro stránku index je úspěšné odstranění citace voláním `QuoteDeleted` metody protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="0724d-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="0724d-153">Pokud se nabídka nenajde pro odstranění, <xref:System.ArgumentNullException> vyvolá se.</span><span class="sxs-lookup"><span data-stu-id="0724d-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="0724d-154">Výjimka je zachycena příkazem [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) a protokolována voláním metody v `QuoteDeleteFailed` protokolovacím nástroji v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="0724d-155">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="0724d-156">Pokud se odstranění citace nepovede, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="0724d-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="0724d-157">Všimněte si, že je tato výjimka součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="0724d-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="0724d-158">LoggerMessage. Definescope –</span><span class="sxs-lookup"><span data-stu-id="0724d-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="0724d-159">[Definescope – (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="0724d-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="0724d-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení povolují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="0724d-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="0724d-161">Stejně jako v případě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="0724d-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0724d-162">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="0724d-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0724d-163">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="0724d-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0724d-164">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="0724d-165">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="0724d-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0724d-166">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0724d-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0724d-167">Definujte [Rozsah protokolu](xref:fundamentals/logging/index#log-scopes) , který se má použít pro řadu zpráv protokolu pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="0724d-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="0724d-168">Ukázková aplikace má tlačítko **Zrušit vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="0724d-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="0724d-169">Tyto nabídky jsou odstraněny po jednom jejich odebráním.</span><span class="sxs-lookup"><span data-stu-id="0724d-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="0724d-170">Pokaždé, když se odstraní citace, `QuoteDeleted` metoda se zavolá do protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="0724d-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="0724d-171">Do těchto zpráv protokolu se přidá rozsah protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="0724d-172">Povolení `IncludeScopes` v části protokolovacího nástroje konzoly *appsettings.jsna*:</span><span class="sxs-lookup"><span data-stu-id="0724d-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="0724d-173">Chcete-li vytvořit rozsah protokolu, přidejte pole pro blokování <xref:System.Func%601> delegáta oboru.</span><span class="sxs-lookup"><span data-stu-id="0724d-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="0724d-174">Ukázková aplikace vytvoří pole s názvem `_allQuotesDeletedScope` (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="0724d-175">Použijte <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="0724d-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="0724d-176">Až tři typy lze zadat pro použití jako argumenty šablony při volání delegáta.</span><span class="sxs-lookup"><span data-stu-id="0724d-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="0724d-177">Ukázková aplikace používá šablonu zprávy, která obsahuje počet odstraněných uvozovek ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="0724d-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="0724d-178">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="0724d-179">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="0724d-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="0724d-180">Ukázková aplikace se převezme v `count` uvozovkách, které se mají odstranit a vrátí `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="0724d-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="0724d-181">Obor zabalí volání rozšíření protokolování v bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="0724d-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="0724d-182">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="0724d-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="0724d-183">Následující výsledek obsahuje tři nabídky odstraněné se zprávou rozsah protokolu, která je součástí:</span><span class="sxs-lookup"><span data-stu-id="0724d-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="0724d-184"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkce vytvářejí delegáty, které umožňují ukládání do mezipaměti, které vyžadují méně přidělení objektů a snižují výpočetní režii v porovnání s [metodami rozšíření protokolovacího](xref:Microsoft.Extensions.Logging.LoggerExtensions)nástroje, jako jsou <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="0724d-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="0724d-185">Pro scénáře protokolování s vysokým výkonem použijte <xref:Microsoft.Extensions.Logging.LoggerMessage> vzor.</span><span class="sxs-lookup"><span data-stu-id="0724d-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="0724d-186"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu v rámci rozšiřujících metod protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="0724d-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="0724d-187">Metody rozšíření protokolovacího nástroje vyžadují "zabalení" (převod) typů hodnot, například `int` do `object` .</span><span class="sxs-lookup"><span data-stu-id="0724d-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="0724d-188"><xref:Microsoft.Extensions.Logging.LoggerMessage>Vzor zabraňuje zabalení pomocí statických <xref:System.Action> polí a metod rozšíření s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="0724d-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="0724d-189">Metody rozšíření protokolovacího nástroje musí při každém zápisu zprávy protokolu analyzovat šablonu zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="0724d-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="0724d-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze při definování zprávy vyžaduje pouze analýzu šablony.</span><span class="sxs-lookup"><span data-stu-id="0724d-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="0724d-191">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0724d-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0724d-192">Ukázková aplikace ukazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkce se základním systémem pro sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="0724d-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="0724d-193">Aplikace přidá a odstraní uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="0724d-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="0724d-194">Při výskytu těchto operací se zprávy protokolu generují pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage> vzoru.</span><span class="sxs-lookup"><span data-stu-id="0724d-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="0724d-195">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="0724d-195">LoggerMessage.Define</span></span>

<span data-ttu-id="0724d-196">[Define (LogLevel, ID události, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří <xref:System.Action> delegáta pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="0724d-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="0724d-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení povolují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="0724d-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="0724d-198">Řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="0724d-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0724d-199">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="0724d-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0724d-200">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="0724d-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0724d-201">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="0724d-202">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="0724d-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0724d-203">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0724d-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0724d-204">Každá zpráva protokolu je <xref:System.Action> držená ve statickém poli vytvořeném pomocí [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="0724d-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="0724d-205">Ukázková aplikace například vytvoří pole pro popis zprávy protokolu pro požadavek GET na stránku indexu (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="0724d-206">Pro <xref:System.Action> zadejte:</span><span class="sxs-lookup"><span data-stu-id="0724d-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="0724d-207">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="0724d-207">The log level.</span></span>
* <span data-ttu-id="0724d-208">Jedinečný identifikátor události ( <xref:Microsoft.Extensions.Logging.EventId> ) s názvem statické metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="0724d-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="0724d-209">Šablona zprávy (pojmenovaný řetězec formátu).</span><span class="sxs-lookup"><span data-stu-id="0724d-209">The message template (named format string).</span></span> 

<span data-ttu-id="0724d-210">Požadavek na stránku index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="0724d-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="0724d-211">Úroveň protokolu do `Information` .</span><span class="sxs-lookup"><span data-stu-id="0724d-211">Log level to `Information`.</span></span>
* <span data-ttu-id="0724d-212">ID události na `1` název `IndexPageRequested` metody</span><span class="sxs-lookup"><span data-stu-id="0724d-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="0724d-213">Šablona zprávy (pojmenovaný řetězec formátu) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="0724d-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="0724d-214">Úložiště strukturovaného protokolování můžou použít název události, když se poskytne s ID události pro rozšíření protokolování.</span><span class="sxs-lookup"><span data-stu-id="0724d-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="0724d-215">[Serilog](https://github.com/serilog/serilog-extensions-logging) například používá název události.</span><span class="sxs-lookup"><span data-stu-id="0724d-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="0724d-216"><xref:System.Action>Je vyvolána prostřednictvím rozšiřující metody silného typu.</span><span class="sxs-lookup"><span data-stu-id="0724d-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="0724d-217">`IndexPageRequested`Metoda zaznamená zprávu pro požadavek na stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0724d-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="0724d-218">`IndexPageRequested`se volá v protokolovacím nástroji v `OnGetAsync` metodě na *stránkách pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0724d-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="0724d-219">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="0724d-220">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="0724d-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="0724d-221">Ukázková aplikace při přidávání nabídky zaznamená řetězec tak, že definuje `string` typ <xref:System.Action> pole:</span><span class="sxs-lookup"><span data-stu-id="0724d-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="0724d-222">Šablona zprávy protokolu delegáta přijímá své zástupné hodnoty z poskytnutých typů.</span><span class="sxs-lookup"><span data-stu-id="0724d-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="0724d-223">Ukázková aplikace definuje delegáta pro přidání nabídky, kde je parametrem nabídky `string` :</span><span class="sxs-lookup"><span data-stu-id="0724d-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="0724d-224">Statická rozšiřující metoda pro přidání nabídky, `QuoteAdded` přijímá hodnotu argumentu citace a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="0724d-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="0724d-225">V modelu stránky indexu stránky (*pages/index. cshtml. cs*) `QuoteAdded` se volá, aby se zaprotokoloval zpráva:</span><span class="sxs-lookup"><span data-stu-id="0724d-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="0724d-226">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="0724d-227">Ukázková aplikace implementuje vzor [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="0724d-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="0724d-228">Informační zpráva se zaznamená do protokolu pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="0724d-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="0724d-229">Pokud je vyvolána výjimka, je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="0724d-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="0724d-230">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimky (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="0724d-231">Všimněte si, jak je výjimka předána delegátovi v `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="0724d-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="0724d-232">V modelu stránky pro stránku index je úspěšné odstranění citace voláním `QuoteDeleted` metody protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="0724d-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="0724d-233">Pokud se nabídka nenajde pro odstranění, <xref:System.ArgumentNullException> vyvolá se.</span><span class="sxs-lookup"><span data-stu-id="0724d-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="0724d-234">Výjimka je zachycena příkazem [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) a protokolována voláním metody v `QuoteDeleteFailed` protokolovacím nástroji v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="0724d-235">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="0724d-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="0724d-236">Pokud se odstranění citace nepovede, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="0724d-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="0724d-237">Všimněte si, že je tato výjimka součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="0724d-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="0724d-238">LoggerMessage. Definescope –</span><span class="sxs-lookup"><span data-stu-id="0724d-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="0724d-239">[Definescope – (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="0724d-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="0724d-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení povolují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (Template).</span><span class="sxs-lookup"><span data-stu-id="0724d-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="0724d-241">Stejně jako v případě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, řetězec poskytnutý <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metodě je šablona, nikoli interpolovaná řetězec.</span><span class="sxs-lookup"><span data-stu-id="0724d-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0724d-242">Zástupné symboly jsou vyplněny v pořadí, v jakém jsou typy zadány.</span><span class="sxs-lookup"><span data-stu-id="0724d-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0724d-243">Zástupné názvy v šabloně by měly být popisné a konzistentní v rámci šablon.</span><span class="sxs-lookup"><span data-stu-id="0724d-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0724d-244">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="0724d-245">Pro názvy zástupných symbolů doporučujeme použít [velká písmena Pascal](/dotnet/standard/design-guidelines/capitalization-conventions) .</span><span class="sxs-lookup"><span data-stu-id="0724d-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0724d-246">Například `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0724d-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0724d-247">Definujte [Rozsah protokolu](xref:fundamentals/logging/index#log-scopes) , který se má použít pro řadu zpráv protokolu pomocí <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="0724d-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="0724d-248">Ukázková aplikace má tlačítko **Zrušit vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="0724d-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="0724d-249">Tyto nabídky jsou odstraněny po jednom jejich odebráním.</span><span class="sxs-lookup"><span data-stu-id="0724d-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="0724d-250">Pokaždé, když se odstraní citace, `QuoteDeleted` metoda se zavolá do protokolovacího nástroje.</span><span class="sxs-lookup"><span data-stu-id="0724d-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="0724d-251">Do těchto zpráv protokolu se přidá rozsah protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="0724d-252">Povolení `IncludeScopes` v části protokolovacího nástroje konzoly *appsettings.jsna*:</span><span class="sxs-lookup"><span data-stu-id="0724d-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="0724d-253">Chcete-li vytvořit rozsah protokolu, přidejte pole pro blokování <xref:System.Func%601> delegáta oboru.</span><span class="sxs-lookup"><span data-stu-id="0724d-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="0724d-254">Ukázková aplikace vytvoří pole s názvem `_allQuotesDeletedScope` (*interní/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0724d-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="0724d-255">Použijte <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="0724d-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="0724d-256">Až tři typy lze zadat pro použití jako argumenty šablony při volání delegáta.</span><span class="sxs-lookup"><span data-stu-id="0724d-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="0724d-257">Ukázková aplikace používá šablonu zprávy, která obsahuje počet odstraněných uvozovek ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="0724d-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="0724d-258">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="0724d-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="0724d-259">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="0724d-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="0724d-260">Ukázková aplikace se převezme v `count` uvozovkách, které se mají odstranit a vrátí `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="0724d-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="0724d-261">Obor zabalí volání rozšíření protokolování v bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="0724d-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="0724d-262">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="0724d-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="0724d-263">Následující výsledek obsahuje tři nabídky odstraněné se zprávou rozsah protokolu, která je součástí:</span><span class="sxs-lookup"><span data-stu-id="0724d-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0724d-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0724d-264">Additional resources</span></span>

* [<span data-ttu-id="0724d-265">protokolování</span><span class="sxs-lookup"><span data-stu-id="0724d-265">Logging</span></span>](xref:fundamentals/logging/index)
