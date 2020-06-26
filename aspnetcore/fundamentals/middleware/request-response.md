---
title: Operace žádosti a odpovědi v ASP.NET Core
author: jkotalik
description: Přečtěte si, jak číst text žádosti a napsat text odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6fc7a115cb0f4696d10bf036eadb59028dfb605
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404129"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operace žádosti a odpovědi v ASP.NET Core

Od [Justin Kotalik](https://github.com/jkotalik)

Tento článek vysvětluje, jak číst z textu žádosti a zapisovat do těla odpovědi. Při zápisu middlewaru může být vyžadován kód pro tyto operace. Mimo psací middleware není vlastní kód všeobecně nutný, protože operace jsou zpracovávány pomocí MVC a Razor stránek.

Pro tělo žádosti a odpovědi jsou k dispozici dvě abstrakce: <xref:System.IO.Stream> a <xref:System.IO.Pipelines.Pipe> . Pro čtení požadavku <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> je <xref:System.IO.Stream> a `HttpRequest.BodyReader` <xref:System.IO.Pipelines.PipeReader> . Pro zápis odpovědí <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> je <xref:System.IO.Stream> a `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter> .

[Kanály](/dotnet/standard/io/pipelines) se doporučují přes streamy. Datové proudy můžou být pro některé jednoduché operace jednodušší, ale kanály mají výkonnou výhodu a je možné je ve většině scénářů snadněji používat. ASP.NET Core začne používat kanály místo interního streamování. Mezi příklady patří:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Datové proudy nejsou odebírány z rozhraní. Streamy se v rámci .NET i nadále používají a mnoho typů datových proudů nemá ekvivalenty kanálů, jako jsou `FileStreams` a `ResponseCompression` .

## <a name="stream-examples"></a>Příklady streamu

Předpokládejme, že cílem je vytvořit middleware, který přečte celý text žádosti jako seznam řetězců a rozdělí na nové řádky. Implementace jednoduchého datového proudu může vypadat jako v následujícím příkladu:

> [!WARNING]
> Následující kód:
> * Slouží k předvedení potíží s nepoužitím kanálu ke čtení textu žádosti.
> * Není určeno pro použití v produkčních aplikacích.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Tento kód funguje, ale došlo k určitým problémům:

* Před připojením k `StringBuilder` , je v příkladu vytvořen jiný řetězec ( `encodedString` ), který je vyvolán okamžitě. K tomuto procesu dochází pro všechny bajty v datovém proudu, takže výsledkem je dodatečné přidělení paměti velikosti celého textu žádosti.
* Tento příklad přečte celý řetězec před rozdělením na nové řádky. Je efektivnější kontrolovat nové řádky v bajtovém poli.

Tady je příklad, který opravuje některé předchozí problémy:

> [!WARNING]
> Následující kód:
> * Slouží k předvedení řešení některých problémů v předchozím kódu, zatímco neřeší všechny problémy.
> * Není určeno pro použití v produkčních aplikacích.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Tento předchozí příklad:

* Neukládá do vyrovnávací paměti celý text žádosti, `StringBuilder` Pokud neexistují žádné znaky nového řádku.
* Nevolá `Split` řetězec.

Stále ale dochází k několika problémům:

* Pokud jsou znaky nového řádku zhuštěné, je většina textu žádosti v řetězci ukládána do vyrovnávací paměti.
* Kód pokračuje v vytváření řetězců ( `remainingString` ) a přidává je do vyrovnávací paměti řetězců, což vede k dodatečnému přidělení.

Tyto problémy jsou fixable, ale kód se postupně častěji komplikovane s malým vylepšením. Kanály poskytují způsob, jak tyto problémy vyřešit s minimální složitou složitostí kódu.

## <a name="pipelines"></a>Pipelines

Následující příklad ukazuje, jak lze stejný scénář zpracovat pomocí [PipeReader](/dotnet/standard/io/pipelines#pipe):

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Tento příklad opravuje mnoho problémů, které měly implementace datových proudů:

* Není nutné ukládat vyrovnávací paměť řetězců `PipeReader` , protože zpracovává bajty, které nebyly použity.
* Zakódované řetězce jsou přímo přidány do seznamu vrácených řetězců.
* Jiné než `ToArray` volání a paměť, která je používána řetězcem, je vytvoření řetězce bez přidělení.

## <a name="adapters"></a>Adaptéry

`Body`Vlastnosti, `BodyReader` a `BodyWriter` jsou k dispozici pro `HttpRequest` a `HttpResponse` . Když nastavíte `Body` jiný datový proud, nová sada adaptérů automaticky přizpůsobí každý typ druhé. Pokud nastavíte `HttpRequest.Body` nový datový proud, `HttpRequest.BodyReader` je automaticky nastaven na nový `PipeReader` , který zabalí `HttpRequest.Body` .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`slouží k označení, že záhlaví jsou neupravitelná a ke spouštění `OnStarting` zpětných volání. Při použití Kestrel jako serveru zavolá `StartAsync` před použitím `PipeReader` záruky, že paměť vrácená funkcí `GetMemory` patří do interní paměti Kestrel, <xref:System.IO.Pipelines.Pipe> nikoli z externí vyrovnávací paměti.

## <a name="additional-resources"></a>Další zdroje

* [System. IO. Pipelines v .NET](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
