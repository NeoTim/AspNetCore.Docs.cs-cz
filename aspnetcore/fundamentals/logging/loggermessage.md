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
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Vysoce výkonné protokolování pomocí LoggerMessage v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>Funkce vytvořit cache delegátů, které vyžadují méně přidělení objektů a sníženou výpočetní režii ve srovnání s [metodami rozšíření protokolů](xref:Microsoft.Extensions.Logging.LoggerExtensions), například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Pro scénáře protokolování s <xref:Microsoft.Extensions.Logging.LoggerMessage> vysokým výkonem použijte vzor.

<xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu oproti metody rozšíření protokolování:

* Metody rozšíření protokolování vyžadují typy hodnot "zabalení" (převod), například `int`na . `object` Vzor <xref:Microsoft.Extensions.Logging.LoggerMessage> zabraňuje zabalení pomocí <xref:System.Action> statických polí a rozšiřujícímetody s parametry silného typu.
* Metody rozšíření protokolovacího nástroje musí analyzovat šablonu zprávy (pojmenovanou formátovací řetězec) při každém zápisu zprávy protokolu. <xref:Microsoft.Extensions.Logging.LoggerMessage>pouze vyžaduje analýzu šablony jednou, když je zpráva definována.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace <xref:Microsoft.Extensions.Logging.LoggerMessage> ukazuje funkce se základním systémem sledování nabídek. Aplikace přidává a odstraňuje uvozovky pomocí databáze v paměti. Při těchto operacích jsou zprávy protokolu <xref:Microsoft.Extensions.Logging.LoggerMessage> generovány pomocí vzoru.

## <a name="loggermessagedefine"></a>LoggerMessage.Definovat

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří delegáta <xref:System.Action> pro protokolování zprávy. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení umožňují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (šabloně).

Řetězec poskytnutý metodě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> je šablona a nikoli interpolovaný řetězec. Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy. Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami. Slouží jako názvy vlastností v rámci strukturovaných dat protokolu. Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions) Například `{Count}`, `{FirstName}`.

Každá zpráva protokolu <xref:System.Action> je držena ve statickém poli vytvořeném [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Ukázková aplikace například vytvoří pole popisující zprávu protokolu pro požadavek GET pro stránku index *(Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Pro <xref:System.Action>, uveďte:

* Úroveň protokolování
* Jedinečný identifikátor události<xref:Microsoft.Extensions.Logging.EventId>( ) s názvem metody statického rozšíření.
* Šablona zprávy (pojmenovaná formátovací řetězec). 

Stránka S dotazem na index ukázkové aplikace nastaví:

* Úroveň protokolu `Information`na .
* Id události `1` s názvem `IndexPageRequested` metody.
* Šablona zprávy (pojmenovaná formátovací řetězec) k řetězci.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Úložiště strukturovaného protokolování mohou používat název události, pokud je dodáván s id události k obohacení protokolování. [Například Serilog](https://github.com/serilog/serilog-extensions-logging) používá název události.

Je <xref:System.Action> vyvolána prostřednictvím metody rozšíření silného typu. Metoda `IndexPageRequested` přihlásí zprávu pro požadavek ZÍSKAT stránku indexu v ukázkové aplikaci:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`je volána na `OnGetAsync` logger v metodě v *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů. Ukázková aplikace přihlásí řetězec při přidání nabídky `string` definováním <xref:System.Action> typu pole:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Šablona protokolu delegáta obdrží své zástupné hodnoty z typů, které jsou k dispozici. Ukázková aplikace definuje delegáta pro přidání nabídky, `string`kde je parametr nabídky :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Metoda statického rozšíření pro `QuoteAdded`přidání nabídky , obdrží hodnotu argumentu nabídky a předá ji <xref:System.Action> delegátovi:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

V modelu stránky Rejstříku (*Pages/Index.cshtml.cs*) `QuoteAdded` se volá zpráva:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Ukázková aplikace implementuje [vzor catch try&ndash;](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky. Informační zpráva je zaznamenána pro úspěšnou operaci odstranění. Při vyvolání výjimky je zaznamenána chybová zpráva pro operaci odstranění. Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimek (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Všimněte si, jak je `QuoteDeleteFailed`výjimka předána delegátovi v :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

V modelu stránky pro index stránky úspěšné odstranění `QuoteDeleted` nabídky volá metodu na protokolování. Pokud není nalezena nabídka pro <xref:System.ArgumentNullException> odstranění, je vyvolána. Výjimka je zachycena příkazem [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) `QuoteDeleteFailed` a zaznamenána voláním metody na protokolovacím paměťci v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Pokud se odstranění nabídky nezdaří, zkontrolujte výstup konzoly aplikace. Všimněte si, že výjimka je součástí zprávy protokolu:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení umožňují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (šabloně).

Stejně jako v <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> případě metody je řetězec <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> poskytnutý metodě šablonou a nikoli interpolovaným řetězcem. Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy. Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami. Slouží jako názvy vlastností v rámci strukturovaných dat protokolu. Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions) Například `{Count}`, `{FirstName}`.

Definujte [obor protokolu,](xref:fundamentals/logging/index#log-scopes) který se použije <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> na řadu zpráv protokolu pomocí metody.

Ukázková aplikace má tlačítko **Vymazat vše** pro odstranění všech nabídek v databázi. Uvozovky jsou odstraněny jejich odebráním jeden po druhém. Pokaždé, když je odstraněn `QuoteDeleted` a uvozovky, metoda je volána na protokolování. Do těchto zpráv protokolu je přidán obor protokolu.

Povolit `IncludeScopes` v sekci protokolování konzoly *appsettings.json*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Chcete-li vytvořit obor protokolu, přidejte pole pro uložení delegáta <xref:System.Func%601> pro obor. Ukázková aplikace vytvoří `_allQuotesDeletedScope` pole s názvem *(Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Slouží <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta. Až tři typy lze zadat pro použití jako argumenty šablony při vyvolání delegáta. Ukázková aplikace používá šablonu zprávy, která obsahuje `int` počet odstraněných uvozovek (typ):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Zadejte statickou metodu rozšíření pro zprávu protokolu. Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy. Ukázková aplikace bere `count` v uvozovkách `_allQuotesDeletedScope`odstranit a vrátí :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Obor zabalí volání rozšíření protokolování v [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace. Následující výsledek ukazuje tři uvozovky odstraněné se zprávou oboru protokolu zahrnuty:

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

<xref:Microsoft.Extensions.Logging.LoggerMessage>Funkce vytvořit cache delegátů, které vyžadují méně přidělení objektů a sníženou výpočetní režii ve srovnání s [metodami rozšíření protokolů](xref:Microsoft.Extensions.Logging.LoggerExtensions), například <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> a <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>. Pro scénáře protokolování s <xref:Microsoft.Extensions.Logging.LoggerMessage> vysokým výkonem použijte vzor.

<xref:Microsoft.Extensions.Logging.LoggerMessage>poskytuje následující výhody výkonu oproti metody rozšíření protokolování:

* Metody rozšíření protokolování vyžadují typy hodnot "zabalení" (převod), například `int`na . `object` Vzor <xref:Microsoft.Extensions.Logging.LoggerMessage> zabraňuje zabalení pomocí <xref:System.Action> statických polí a rozšiřujícímetody s parametry silného typu.
* Metody rozšíření protokolovacího nástroje musí analyzovat šablonu zprávy (pojmenovanou formátovací řetězec) při každém zápisu zprávy protokolu. <xref:Microsoft.Extensions.Logging.LoggerMessage>pouze vyžaduje analýzu šablony jednou, když je zpráva definována.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace <xref:Microsoft.Extensions.Logging.LoggerMessage> ukazuje funkce se základním systémem sledování nabídek. Aplikace přidává a odstraňuje uvozovky pomocí databáze v paměti. Při těchto operacích jsou zprávy protokolu <xref:Microsoft.Extensions.Logging.LoggerMessage> generovány pomocí vzoru.

## <a name="loggermessagedefine"></a>LoggerMessage.Definovat

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) vytvoří delegáta <xref:System.Action> pro protokolování zprávy. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>přetížení umožňují předání až šesti parametrů typu pojmenovanému formátovacímu řetězci (šabloně).

Řetězec poskytnutý metodě <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> je šablona a nikoli interpolovaný řetězec. Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy. Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami. Slouží jako názvy vlastností v rámci strukturovaných dat protokolu. Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions) Například `{Count}`, `{FirstName}`.

Každá zpráva protokolu <xref:System.Action> je držena ve statickém poli vytvořeném [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Ukázková aplikace například vytvoří pole popisující zprávu protokolu pro požadavek GET pro stránku index *(Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Pro <xref:System.Action>, uveďte:

* Úroveň protokolování
* Jedinečný identifikátor události<xref:Microsoft.Extensions.Logging.EventId>( ) s názvem metody statického rozšíření.
* Šablona zprávy (pojmenovaná formátovací řetězec). 

Stránka S dotazem na index ukázkové aplikace nastaví:

* Úroveň protokolu `Information`na .
* Id události `1` s názvem `IndexPageRequested` metody.
* Šablona zprávy (pojmenovaná formátovací řetězec) k řetězci.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Úložiště strukturovaného protokolování mohou používat název události, pokud je dodáván s id události k obohacení protokolování. [Například Serilog](https://github.com/serilog/serilog-extensions-logging) používá název události.

Je <xref:System.Action> vyvolána prostřednictvím metody rozšíření silného typu. Metoda `IndexPageRequested` přihlásí zprávu pro požadavek ZÍSKAT stránku indexu v ukázkové aplikaci:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`je volána na `OnGetAsync` logger v metodě v *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Chcete-li předat parametry do zprávy protokolu, definujte při vytváření statického pole až šest typů. Ukázková aplikace přihlásí řetězec při přidání nabídky `string` definováním <xref:System.Action> typu pole:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Šablona protokolu delegáta obdrží své zástupné hodnoty z typů, které jsou k dispozici. Ukázková aplikace definuje delegáta pro přidání nabídky, `string`kde je parametr nabídky :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Metoda statického rozšíření pro `QuoteAdded`přidání nabídky , obdrží hodnotu argumentu nabídky a předá ji <xref:System.Action> delegátovi:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

V modelu stránky Rejstříku (*Pages/Index.cshtml.cs*) `QuoteAdded` se volá zpráva:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Ukázková aplikace implementuje [vzor catch try&ndash;](/dotnet/csharp/language-reference/keywords/try-catch) pro odstranění nabídky. Informační zpráva je zaznamenána pro úspěšnou operaci odstranění. Při vyvolání výjimky je zaznamenána chybová zpráva pro operaci odstranění. Zpráva protokolu pro neúspěšnou operaci odstranění zahrnuje trasování zásobníku výjimek (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Všimněte si, jak je `QuoteDeleteFailed`výjimka předána delegátovi v :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

V modelu stránky pro index stránky úspěšné odstranění `QuoteDeleted` nabídky volá metodu na protokolování. Pokud není nalezena nabídka pro <xref:System.ArgumentNullException> odstranění, je vyvolána. Výjimka je zachycena příkazem [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) `QuoteDeleteFailed` a zaznamenána voláním metody na protokolovacím paměťci v bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Po úspěšném odstranění nabídky zkontrolujte výstup konzoly aplikace:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Pokud se odstranění nabídky nezdaří, zkontrolujte výstup konzoly aplikace. Všimněte si, že výjimka je součástí zprávy protokolu:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) vytvoří <xref:System.Func%601> delegáta pro definování [oboru protokolu](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>přetížení umožňují předání až tří parametrů typu pojmenovanému formátovacímu řetězci (šabloně).

Stejně jako v <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> případě metody je řetězec <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> poskytnutý metodě šablonou a nikoli interpolovaným řetězcem. Zástupné symboly jsou vyplněny v pořadí, ve které jsou zadány typy. Zástupné názvy v šabloně by měly být popisné a konzistentní napříč šablonami. Slouží jako názvy vlastností v rámci strukturovaných dat protokolu. Pro zástupné názvy doporučujeme [kryty Pascal.](/dotnet/standard/design-guidelines/capitalization-conventions) Například `{Count}`, `{FirstName}`.

Definujte [obor protokolu,](xref:fundamentals/logging/index#log-scopes) který se použije <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> na řadu zpráv protokolu pomocí metody.

Ukázková aplikace má tlačítko **Vymazat vše** pro odstranění všech nabídek v databázi. Uvozovky jsou odstraněny jejich odebráním jeden po druhém. Pokaždé, když je odstraněn `QuoteDeleted` a uvozovky, metoda je volána na protokolování. Do těchto zpráv protokolu je přidán obor protokolu.

Povolit `IncludeScopes` v sekci protokolování konzoly *appsettings.json*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Chcete-li vytvořit obor protokolu, přidejte pole pro uložení delegáta <xref:System.Func%601> pro obor. Ukázková aplikace vytvoří `_allQuotesDeletedScope` pole s názvem *(Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Slouží <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> k vytvoření delegáta. Až tři typy lze zadat pro použití jako argumenty šablony při vyvolání delegáta. Ukázková aplikace používá šablonu zprávy, která obsahuje `int` počet odstraněných uvozovek (typ):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Zadejte statickou metodu rozšíření pro zprávu protokolu. Zahrňte všechny parametry typu pro pojmenované vlastnosti, které se zobrazí v šabloně zprávy. Ukázková aplikace bere `count` v uvozovkách `_allQuotesDeletedScope`odstranit a vrátí :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Obor zabalí volání rozšíření protokolování v [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Zkontrolujte zprávy protokolu ve výstupu konzoly aplikace. Následující výsledek ukazuje tři uvozovky odstraněné se zprávou oboru protokolu zahrnuty:

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

## <a name="additional-resources"></a>Další zdroje

* [Protokolování](xref:fundamentals/logging/index)
