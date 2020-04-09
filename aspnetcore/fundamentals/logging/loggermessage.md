---
title: Vysoce výkonné protokolování pomocí LoggerMessage v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí LoggerMessage vytvořit delegáty s mezipamětí, které vyžadují méně přidělení objektů pro scénáře protokolování s vysokým výkonem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 48ebba69b5c15a0f9a42f7f6b3d2c1fcb0a2211c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663217"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="47f39-103">Vysoce výkonné protokolování pomocí LoggerMessage v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47f39-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47f39-104"><xref:Microsoft.Extensions.Logging.LoggerMessage>Funkce vytvořit cache delegátů, které vyžadují méně přidělení objektů a sníženou výpočetní režii ve srovnání s [metodami rozšíření protokolů](xref:Microsoft.Extensions.Logging.LoggerExtensions), například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="47f39-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="47f39-105">Pro scénáře protokolování s <xref:Microsoft.Extensions.Logging.LoggerMessage> vysokým výkonem použijte vzor.</span><span class="sxs-lookup"><span data-stu-id="47f39-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="47f39-106"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu oproti metody rozšíření protokolování:</span><span class="sxs-lookup"><span data-stu-id="47f39-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="47f39-107">Metody rozšíření protokolování vyžadují typy hodnot "zabalení" (převod), například `int`na . `object`</span><span class="sxs-lookup"><span data-stu-id="47f39-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="47f39-108">Vzor <xref:Microsoft.Extensions.Logging.LoggerMessage> zabraňuje zabalení pomocí <xref:System.Action> statických polí a rozšiřujícímetody s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="47f39-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="47f39-109">Metody rozšíření protokolovacího nástroje musí analyzovat šablonu zprávy (pojmenovanou formátovací řetězec) při každém zápisu zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="47f39-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze vyžaduje analýzu šablony jednou, když je zpráva definována.</span><span class="sxs-lookup"><span data-stu-id="47f39-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="47f39-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="47f39-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="47f39-112">Ukázková aplikace <xref:Microsoft.Extensions.Logging.LoggerMessage> ukazuje funkce se základním systémem sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="47f39-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="47f39-113">Aplikace přidává a odstraňuje uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="47f39-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="47f39-114">Při těchto operacích jsou zprávy protokolu <xref:Microsoft.Extensions.Logging.LoggerMessage> generovány pomocí vzoru.</span><span class="sxs-lookup"><span data-stu-id="47f39-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="47f39-115">LoggerMessage.Definovat</span><span class="sxs-lookup"><span data-stu-id="47f39-115">LoggerMessage.Define</span></span>

<span data-ttu-id="47f39-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří delegáta <xref:System.Action> pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="47f39-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="47f39-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení umožňují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (šabloně).</span><span class="sxs-lookup"><span data-stu-id="47f39-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="47f39-118">Řetězec poskytnutý metodě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> je šablona a nikoli interpolovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="47f39-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="47f39-119">Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy.</span><span class="sxs-lookup"><span data-stu-id="47f39-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="47f39-120">Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami.</span><span class="sxs-lookup"><span data-stu-id="47f39-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="47f39-121">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="47f39-122">Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions)</span><span class="sxs-lookup"><span data-stu-id="47f39-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="47f39-123">Například `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="47f39-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="47f39-124">Každá zpráva protokolu <xref:System.Action> je držena ve statickém poli vytvořeném [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="47f39-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="47f39-125">Ukázková aplikace například vytvoří pole popisující zprávu protokolu pro požadavek GET pro stránku index *(Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="47f39-126">Pro <xref:System.Action>, uveďte:</span><span class="sxs-lookup"><span data-stu-id="47f39-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="47f39-127">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="47f39-127">The log level.</span></span>
* <span data-ttu-id="47f39-128">Jedinečný identifikátor události<xref:Microsoft.Extensions.Logging.EventId>( ) s názvem metody statického rozšíření.</span><span class="sxs-lookup"><span data-stu-id="47f39-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="47f39-129">Šablona zprávy (pojmenovaná formátovací řetězec).</span><span class="sxs-lookup"><span data-stu-id="47f39-129">The message template (named format string).</span></span> 

<span data-ttu-id="47f39-130">Stránka S dotazem na index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="47f39-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="47f39-131">Úroveň protokolu `Information`na .</span><span class="sxs-lookup"><span data-stu-id="47f39-131">Log level to `Information`.</span></span>
* <span data-ttu-id="47f39-132">Id události `1` s názvem `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="47f39-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="47f39-133">Šablona zprávy (pojmenovaná formátovací řetězec) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="47f39-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="47f39-134">Úložiště strukturovaného protokolování mohou používat název události, pokud je dodáván s id události k obohacení protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="47f39-135">[Například Serilog](https://github.com/serilog/serilog-extensions-logging) používá název události.</span><span class="sxs-lookup"><span data-stu-id="47f39-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="47f39-136">Je <xref:System.Action> vyvolána prostřednictvím metody rozšíření silného typu.</span><span class="sxs-lookup"><span data-stu-id="47f39-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="47f39-137">Metoda `IndexPageRequested` přihlásí zprávu pro požadavek ZÍSKAT stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="47f39-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="47f39-138">`IndexPageRequested`je volána na `OnGetAsync` logger v metodě v *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="47f39-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="47f39-139">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="47f39-140">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="47f39-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="47f39-141">Ukázková aplikace přihlásí řetězec při přidání nabídky `string` definováním <xref:System.Action> typu pole:</span><span class="sxs-lookup"><span data-stu-id="47f39-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="47f39-142">Šablona protokolu delegáta obdrží své zástupné hodnoty z typů, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="47f39-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="47f39-143">Ukázková aplikace definuje delegáta pro přidání nabídky, `string`kde je parametr nabídky :</span><span class="sxs-lookup"><span data-stu-id="47f39-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="47f39-144">Metoda statického rozšíření pro `QuoteAdded`přidání nabídky , obdrží hodnotu argumentu nabídky a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="47f39-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="47f39-145">V modelu stránky Rejstříku (*Pages/Index.cshtml.cs*) `QuoteAdded` se volá zpráva:</span><span class="sxs-lookup"><span data-stu-id="47f39-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="47f39-146">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="47f39-147">Ukázková aplikace implementuje [vzor catch try&ndash;](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="47f39-147">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="47f39-148">Informační zpráva je zaznamenána pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="47f39-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="47f39-149">Při vyvolání výjimky je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="47f39-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="47f39-150">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimek (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="47f39-151">Všimněte si, jak je `QuoteDeleteFailed`výjimka předána delegátovi v :</span><span class="sxs-lookup"><span data-stu-id="47f39-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="47f39-152">V modelu stránky pro index stránky úspěšné odstranění `QuoteDeleted` nabídky volá metodu na protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="47f39-153">Pokud není nalezena nabídka pro <xref:System.ArgumentNullException> odstranění, je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="47f39-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="47f39-154">Výjimka je zachycena příkazem [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) `QuoteDeleteFailed` a zaznamenána voláním metody na protokolovacím paměťci v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-154">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="47f39-155">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="47f39-156">Pokud se odstranění nabídky nezdaří, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="47f39-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="47f39-157">Všimněte si, že výjimka je součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="47f39-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="47f39-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="47f39-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="47f39-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="47f39-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="47f39-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení umožňují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (šabloně).</span><span class="sxs-lookup"><span data-stu-id="47f39-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="47f39-161">Stejně jako v <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> případě metody je řetězec <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> poskytnutý metodě šablonou a nikoli interpolovaným řetězcem.</span><span class="sxs-lookup"><span data-stu-id="47f39-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="47f39-162">Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy.</span><span class="sxs-lookup"><span data-stu-id="47f39-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="47f39-163">Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami.</span><span class="sxs-lookup"><span data-stu-id="47f39-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="47f39-164">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="47f39-165">Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions)</span><span class="sxs-lookup"><span data-stu-id="47f39-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="47f39-166">Například `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="47f39-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="47f39-167">Definujte [obor protokolu,](xref:fundamentals/logging/index#log-scopes) který se použije <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> na řadu zpráv protokolu pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="47f39-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="47f39-168">Ukázková aplikace má tlačítko **Vymazat vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="47f39-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="47f39-169">Uvozovky jsou odstraněny jejich odebráním jeden po druhém.</span><span class="sxs-lookup"><span data-stu-id="47f39-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="47f39-170">Pokaždé, když je odstraněn `QuoteDeleted` a uvozovky, metoda je volána na protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="47f39-171">Do těchto zpráv protokolu je přidán obor protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="47f39-172">Povolit `IncludeScopes` v sekci protokolování konzoly *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47f39-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="47f39-173">Chcete-li vytvořit obor protokolu, přidejte pole pro uložení delegáta <xref:System.Func%601> pro obor.</span><span class="sxs-lookup"><span data-stu-id="47f39-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="47f39-174">Ukázková aplikace vytvoří `_allQuotesDeletedScope` pole s názvem *(Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="47f39-175">Slouží <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="47f39-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="47f39-176">Až tři typy lze zadat pro použití jako argumenty šablony při vyvolání delegáta.</span><span class="sxs-lookup"><span data-stu-id="47f39-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="47f39-177">Ukázková aplikace používá šablonu zprávy, která obsahuje `int` počet odstraněných uvozovek (typ):</span><span class="sxs-lookup"><span data-stu-id="47f39-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="47f39-178">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="47f39-179">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="47f39-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="47f39-180">Ukázková aplikace bere `count` v uvozovkách `_allQuotesDeletedScope`odstranit a vrátí :</span><span class="sxs-lookup"><span data-stu-id="47f39-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="47f39-181">Obor zabalí volání rozšíření protokolování v [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:</span><span class="sxs-lookup"><span data-stu-id="47f39-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="47f39-182">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="47f39-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="47f39-183">Následující výsledek ukazuje tři uvozovky odstraněné se zprávou oboru protokolu zahrnuty:</span><span class="sxs-lookup"><span data-stu-id="47f39-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="47f39-184"><xref:Microsoft.Extensions.Logging.LoggerMessage>Funkce vytvořit cache delegátů, které vyžadují méně přidělení objektů a sníženou výpočetní režii ve srovnání s [metodami rozšíření protokolů](xref:Microsoft.Extensions.Logging.LoggerExtensions), například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="47f39-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="47f39-185">Pro scénáře protokolování s <xref:Microsoft.Extensions.Logging.LoggerMessage> vysokým výkonem použijte vzor.</span><span class="sxs-lookup"><span data-stu-id="47f39-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="47f39-186"><xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu oproti metody rozšíření protokolování:</span><span class="sxs-lookup"><span data-stu-id="47f39-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="47f39-187">Metody rozšíření protokolování vyžadují typy hodnot "zabalení" (převod), například `int`na . `object`</span><span class="sxs-lookup"><span data-stu-id="47f39-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="47f39-188">Vzor <xref:Microsoft.Extensions.Logging.LoggerMessage> zabraňuje zabalení pomocí <xref:System.Action> statických polí a rozšiřujícímetody s parametry silného typu.</span><span class="sxs-lookup"><span data-stu-id="47f39-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="47f39-189">Metody rozšíření protokolovacího nástroje musí analyzovat šablonu zprávy (pojmenovanou formátovací řetězec) při každém zápisu zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="47f39-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>pouze vyžaduje analýzu šablony jednou, když je zpráva definována.</span><span class="sxs-lookup"><span data-stu-id="47f39-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="47f39-191">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="47f39-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="47f39-192">Ukázková aplikace <xref:Microsoft.Extensions.Logging.LoggerMessage> ukazuje funkce se základním systémem sledování nabídek.</span><span class="sxs-lookup"><span data-stu-id="47f39-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="47f39-193">Aplikace přidává a odstraňuje uvozovky pomocí databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="47f39-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="47f39-194">Při těchto operacích jsou zprávy protokolu <xref:Microsoft.Extensions.Logging.LoggerMessage> generovány pomocí vzoru.</span><span class="sxs-lookup"><span data-stu-id="47f39-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="47f39-195">LoggerMessage.Definovat</span><span class="sxs-lookup"><span data-stu-id="47f39-195">LoggerMessage.Define</span></span>

<span data-ttu-id="47f39-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří delegáta <xref:System.Action> pro protokolování zprávy.</span><span class="sxs-lookup"><span data-stu-id="47f39-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="47f39-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení umožňují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (šabloně).</span><span class="sxs-lookup"><span data-stu-id="47f39-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="47f39-198">Řetězec poskytnutý metodě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> je šablona a nikoli interpolovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="47f39-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="47f39-199">Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy.</span><span class="sxs-lookup"><span data-stu-id="47f39-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="47f39-200">Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami.</span><span class="sxs-lookup"><span data-stu-id="47f39-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="47f39-201">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="47f39-202">Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions)</span><span class="sxs-lookup"><span data-stu-id="47f39-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="47f39-203">Například `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="47f39-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="47f39-204">Každá zpráva protokolu <xref:System.Action> je držena ve statickém poli vytvořeném [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="47f39-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="47f39-205">Ukázková aplikace například vytvoří pole popisující zprávu protokolu pro požadavek GET pro stránku index *(Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="47f39-206">Pro <xref:System.Action>, uveďte:</span><span class="sxs-lookup"><span data-stu-id="47f39-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="47f39-207">Úroveň protokolování</span><span class="sxs-lookup"><span data-stu-id="47f39-207">The log level.</span></span>
* <span data-ttu-id="47f39-208">Jedinečný identifikátor události<xref:Microsoft.Extensions.Logging.EventId>( ) s názvem metody statického rozšíření.</span><span class="sxs-lookup"><span data-stu-id="47f39-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="47f39-209">Šablona zprávy (pojmenovaná formátovací řetězec).</span><span class="sxs-lookup"><span data-stu-id="47f39-209">The message template (named format string).</span></span> 

<span data-ttu-id="47f39-210">Stránka S dotazem na index ukázkové aplikace nastaví:</span><span class="sxs-lookup"><span data-stu-id="47f39-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="47f39-211">Úroveň protokolu `Information`na .</span><span class="sxs-lookup"><span data-stu-id="47f39-211">Log level to `Information`.</span></span>
* <span data-ttu-id="47f39-212">Id události `1` s názvem `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="47f39-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="47f39-213">Šablona zprávy (pojmenovaná formátovací řetězec) k řetězci.</span><span class="sxs-lookup"><span data-stu-id="47f39-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="47f39-214">Úložiště strukturovaného protokolování mohou používat název události, pokud je dodáván s id události k obohacení protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="47f39-215">[Například Serilog](https://github.com/serilog/serilog-extensions-logging) používá název události.</span><span class="sxs-lookup"><span data-stu-id="47f39-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="47f39-216">Je <xref:System.Action> vyvolána prostřednictvím metody rozšíření silného typu.</span><span class="sxs-lookup"><span data-stu-id="47f39-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="47f39-217">Metoda `IndexPageRequested` přihlásí zprávu pro požadavek ZÍSKAT stránku indexu v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="47f39-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="47f39-218">`IndexPageRequested`je volána na `OnGetAsync` logger v metodě v *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="47f39-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="47f39-219">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="47f39-220">Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů.</span><span class="sxs-lookup"><span data-stu-id="47f39-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="47f39-221">Ukázková aplikace přihlásí řetězec při přidání nabídky `string` definováním <xref:System.Action> typu pole:</span><span class="sxs-lookup"><span data-stu-id="47f39-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="47f39-222">Šablona protokolu delegáta obdrží své zástupné hodnoty z typů, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="47f39-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="47f39-223">Ukázková aplikace definuje delegáta pro přidání nabídky, `string`kde je parametr nabídky :</span><span class="sxs-lookup"><span data-stu-id="47f39-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="47f39-224">Metoda statického rozšíření pro `QuoteAdded`přidání nabídky , obdrží hodnotu argumentu nabídky a předá ji <xref:System.Action> delegátovi:</span><span class="sxs-lookup"><span data-stu-id="47f39-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="47f39-225">V modelu stránky Rejstříku (*Pages/Index.cshtml.cs*) `QuoteAdded` se volá zpráva:</span><span class="sxs-lookup"><span data-stu-id="47f39-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="47f39-226">Zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="47f39-227">Ukázková aplikace implementuje [vzor catch try&ndash;](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky.</span><span class="sxs-lookup"><span data-stu-id="47f39-227">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="47f39-228">Informační zpráva je zaznamenána pro úspěšnou operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="47f39-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="47f39-229">Při vyvolání výjimky je zaznamenána chybová zpráva pro operaci odstranění.</span><span class="sxs-lookup"><span data-stu-id="47f39-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="47f39-230">Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimek (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="47f39-231">Všimněte si, jak je `QuoteDeleteFailed`výjimka předána delegátovi v :</span><span class="sxs-lookup"><span data-stu-id="47f39-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="47f39-232">V modelu stránky pro index stránky úspěšné odstranění `QuoteDeleted` nabídky volá metodu na protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="47f39-233">Pokud není nalezena nabídka pro <xref:System.ArgumentNullException> odstranění, je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="47f39-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="47f39-234">Výjimka je zachycena příkazem [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) `QuoteDeleteFailed` a zaznamenána voláním metody na protokolovacím paměťci v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-234">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="47f39-235">Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:</span><span class="sxs-lookup"><span data-stu-id="47f39-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="47f39-236">Pokud se odstranění nabídky nezdaří, zkontrolujte výstup konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="47f39-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="47f39-237">Všimněte si, že výjimka je součástí zprávy protokolu:</span><span class="sxs-lookup"><span data-stu-id="47f39-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="47f39-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="47f39-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="47f39-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="47f39-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="47f39-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení umožňují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (šabloně).</span><span class="sxs-lookup"><span data-stu-id="47f39-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="47f39-241">Stejně jako v <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> případě metody je řetězec <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> poskytnutý metodě šablonou a nikoli interpolovaným řetězcem.</span><span class="sxs-lookup"><span data-stu-id="47f39-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="47f39-242">Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy.</span><span class="sxs-lookup"><span data-stu-id="47f39-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="47f39-243">Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami.</span><span class="sxs-lookup"><span data-stu-id="47f39-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="47f39-244">Slouží jako názvy vlastností v rámci strukturovaných dat protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="47f39-245">Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions)</span><span class="sxs-lookup"><span data-stu-id="47f39-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="47f39-246">Například `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="47f39-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="47f39-247">Definujte [obor protokolu,](xref:fundamentals/logging/index#log-scopes) který se použije <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> na řadu zpráv protokolu pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="47f39-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="47f39-248">Ukázková aplikace má tlačítko **Vymazat vše** pro odstranění všech nabídek v databázi.</span><span class="sxs-lookup"><span data-stu-id="47f39-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="47f39-249">Uvozovky jsou odstraněny jejich odebráním jeden po druhém.</span><span class="sxs-lookup"><span data-stu-id="47f39-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="47f39-250">Pokaždé, když je odstraněn `QuoteDeleted` a uvozovky, metoda je volána na protokolování.</span><span class="sxs-lookup"><span data-stu-id="47f39-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="47f39-251">Do těchto zpráv protokolu je přidán obor protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="47f39-252">Povolit `IncludeScopes` v sekci protokolování konzoly *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47f39-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="47f39-253">Chcete-li vytvořit obor protokolu, přidejte pole pro uložení delegáta <xref:System.Func%601> pro obor.</span><span class="sxs-lookup"><span data-stu-id="47f39-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="47f39-254">Ukázková aplikace vytvoří `_allQuotesDeletedScope` pole s názvem *(Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="47f39-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="47f39-255">Slouží <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta.</span><span class="sxs-lookup"><span data-stu-id="47f39-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="47f39-256">Až tři typy lze zadat pro použití jako argumenty šablony při vyvolání delegáta.</span><span class="sxs-lookup"><span data-stu-id="47f39-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="47f39-257">Ukázková aplikace používá šablonu zprávy, která obsahuje `int` počet odstraněných uvozovek (typ):</span><span class="sxs-lookup"><span data-stu-id="47f39-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="47f39-258">Zadejte statickou metodu rozšíření pro zprávu protokolu.</span><span class="sxs-lookup"><span data-stu-id="47f39-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="47f39-259">Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy.</span><span class="sxs-lookup"><span data-stu-id="47f39-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="47f39-260">Ukázková aplikace bere `count` v uvozovkách `_allQuotesDeletedScope`odstranit a vrátí :</span><span class="sxs-lookup"><span data-stu-id="47f39-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="47f39-261">Obor zabalí volání rozšíření protokolování v [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:</span><span class="sxs-lookup"><span data-stu-id="47f39-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="47f39-262">Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace.</span><span class="sxs-lookup"><span data-stu-id="47f39-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="47f39-263">Následující výsledek ukazuje tři uvozovky odstraněné se zprávou oboru protokolu zahrnuty:</span><span class="sxs-lookup"><span data-stu-id="47f39-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="47f39-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="47f39-264">Additional resources</span></span>

* [<span data-ttu-id="47f39-265">Protokolování</span><span class="sxs-lookup"><span data-stu-id="47f39-265">Logging</span></span>](xref:fundamentals/logging/index)
