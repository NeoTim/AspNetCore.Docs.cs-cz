---
title: Operace požadavků a odpovědí v ASP.NET Core
author: jkotalik
description: Naučte se číst tělo požadavku a napsat tělo odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667214"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operace požadavků a odpovědí v ASP.NET Core

Podle [Justin Kotalik](https://github.com/jkotalik)

Tento článek vysvětluje, jak číst z těla požadavku a zapisovat do těla odpovědi. Kód pro tyto operace může být vyžadovánpři psaní middleware. Mimo psaní middleware, vlastní kód není obecně vyžadováno, protože operace jsou zpracovány MVC a Razor stránky.

Existují dvě abstrakce pro tělo žádosti <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>a odpovědi: a . Pro čtení požadavku [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) <xref:System.IO.Stream>je `HttpRequest.BodyReader` a <xref:System.IO.Pipelines.PipeReader>, a je . Pro psaní odpovědi [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) <xref:System.IO.Stream>je `HttpResponse.BodyWriter` a <xref:System.IO.Pipelines.PipeWriter>je .

[Kanály](/dotnet/standard/io/pipelines) se doporučují přes datové proudy. Datové proudy lze snadněji použít pro některé jednoduché operace, ale kanály mají výhodu výkonu a jsou snadněji použitelné ve většině scénářů. ASP.NET Core začíná používat kanály namísto datových proudů interně. Příklady obsahují:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Datové proudy nejsou odebírány z rámce. Datové proudy se nadále používají v celé síti .NET a mnoho `FileStreams` `ResponseCompression`typů datových proudů nemá ekvivalenty kanálu, například a .

## <a name="stream-examples"></a>Příklady streamování

Předpokládejme, že cílem je vytvořit middleware, který přečte celé tělo požadavku jako seznam řetězců, rozdělení na nové řádky. Jednoduchá implementace datového proudu může vypadat jako následující příklad:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Tento kód funguje, ale existují některé problémy:

* Před připojením `StringBuilder`k , příklad vytvoří`encodedString`jiný řetězec ( ), který je zahozen okamžitě. Tento proces probíhá pro všechny bajty v datovém proudu, takže výsledkem je přidělení další paměti velikost celého těla požadavku.
* Příklad přečte celý řetězec před rozdělením na nové řádky. Je efektivnější zkontrolovat nové řádky v poli bajtů.

Tady je příklad, který opravuje některé z předchozích problémů:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Tento předchozí příklad:

* Není vyrovnávací paměti celé tělo `StringBuilder` požadavku v, pokud neexistují žádné znaky nového řádku.
* Nevolá `Split` na provázku.

Stále však existuje několik problémů:

* Pokud jsou znaky nového řádku řídké, velká část těla požadavku je v řetězci uložena do vyrovnávací paměti.
* Kód pokračuje ve vytváření`remainingString`řetězců ( ) a přidá je do vyrovnávací paměti řetězce, což má za následek další přidělení.

Tyto problémy jsou opravitelné, ale kód se stává stále složitější s malým zlepšením. Kanály poskytují způsob, jak vyřešit tyto problémy s minimální složitostí kódu.

## <a name="pipelines"></a>Kanály

Následující příklad ukazuje, jak lze stejný `PipeReader`scénář zpracovat pomocí :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Tento příklad opravuje mnoho problémů, které implementace datových proudů měl:

* Není potřeba vyrovnávací paměti řetězce, `PipeReader` protože popisovače bajtů, které nebyly použity.
* Kódované řetězce jsou přímo přidány do seznamu vrácených řetězců.
* Vytvoření řetězce je bez přidělení kromě paměti používané `ToArray()` řetězcem (s výjimkou volání).

## <a name="adapters"></a>Adaptéry

K `Body` `BodyReader/BodyWriter` dispozici jsou `HttpRequest` i `HttpResponse`vlastnosti pro a . Pokud nastavíte `Body` jiný datový proud, nová sada adaptérů automaticky přizpůsobí každý typ druhému. Pokud `HttpRequest.Body` nastavíte nový `HttpRequest.BodyReader` datový proud, je `PipeReader` automaticky `HttpRequest.Body`nastavena na nový, který obtéká .

## <a name="startasync"></a>Spuštění synchronizace

`HttpResponse.StartAsync`se používá k označení, že záhlaví jsou `OnStarting` nemodifikovatelné a ke spuštění zpětná volání. Při použití Kestrel jako `StartAsync` server, `PipeReader` volání před použitím `GetMemory` záruky, že paměť vrácena patří kestrel vnitřní <xref:System.IO.Pipelines.Pipe> spíše než externí vyrovnávací paměti.

## <a name="additional-resources"></a>Další zdroje

* [Představujeme System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
