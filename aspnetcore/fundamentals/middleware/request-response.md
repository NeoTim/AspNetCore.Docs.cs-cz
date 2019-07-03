---
title: Operace požadavku a odpovědi v ASP.NET Core
author: jkotalik
description: Zjistěte, jak datovou část požadavku čtení a zápis datové části odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 0c321dad256e239b61907980c09d2c088c1407ff
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538576"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operace požadavku a odpovědi v ASP.NET Core

Podle [Justin Kotalik](https://github.com/jkotalik)

Tento článek vysvětluje, jak číst z textu požadavku a zapisovat do datové části odpovědi. Můžete napsat kód pro tyto operace při psaní middlewaru. V opačném případě obvykle není nutné tento kód napsat, protože operace jsou zpracovávány MVC a stránky Razor.

V ASP.NET Core 3.0 jsou dvě abstrakce pro těla požadavku a odpovědi: <xref:System.IO.Stream> a <xref:System.IO.Pipelines.Pipe>. Pro žádosti o čtení [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) je <xref:System.IO.Stream>, a `HttpRequest.BodyPipe` je <xref:System.IO.Pipelines.PipeReader>. Pro psaní odpovědi [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) je `HttpResponse.BodyPipe` je <xref:System.IO.Pipelines.PipeWriter>.

Doporučujeme, abyste kanálů prostřednictvím datových proudů. Datové proudy může být jednodušší použít pro některé jednoduché operace, ale kanály mají využívat výkon a usnadňuje používání ve většině scénářů. 3\.0 ASP.NET Core spouští kanály nahrazujícím datové proudy interně. Příklady:

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

Datové proudy nejsou opouštěl. Nadále používat v rámci .NET a mnoho typů datového proudu nemají ekvivalenty kanálu, jako je třeba `FileStreams` a `ResponseCompression`.

## <a name="stream-examples"></a>Příklady Stream

Předpokládejme, že chcete vytvořit middleware, který čte celého těla požadavku jako seznam řetězců, rozdělení na nových řádcích. Implementace jednoduchého datový proud může vypadat jako v následujícím příkladu:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

Tento kód funguje, ale dojde k problémům:

- Před připojením k `StringBuilder`, v příkladu se vytvoří další řetězec (`encodedString`), která je okamžitě zahozeny. Tento proces se provádí pro všechny bajty v datovém proudu, takže výsledkem je paměť navíc přidělení velikosti celého těla požadavku.
- Tento příklad načte celý řetězec před rozdělením na nových řádcích. Bylo by mnohem efektivnější ke kontrole pro nové řádky v bajtové pole.

Tady je příklad, který řeší některé z těchto problémů:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

V tomto příkladu:

- Není ve vyrovnávací paměti textu celého žádosti v `StringBuilder` Pokud nejsou k dispozici žádné znaky nového řádku.
- Nevolá `Split` na řetězec.

Existují však stále se několik problémů:

- Pokud jsou znaky nového řádku zhuštěný, velkou část textu požadavku do vyrovnávací paměti v řetězci.
- Přesto vytvoří řetězce (`remainingString`) a přidá je do vyrovnávací paměti řetězce, což vede k další přidělení.

Je možné opravit tyto problémy, ale kód je stává čím dál tím víc složité díky vylepšení malý. Kanály poskytují způsob, jak vyřešit tyto problémy s minimem kódování složitost.

## <a name="pipelines"></a>Kanály

Následující příklad ukazuje, jak můžete stejný scénář se určují pomocí šablon `PipeReader`:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

V tomto příkladu řeší řadu problémů, které měly datové proudy implementace:

- Není nutné pro vyrovnávací paměti řetězce vzhledem k tomu, `PipeReader` zpracovává bajtů, které nebyly použity.
- Kódovaný řetězec přímo se přidají do seznamu vráceného řetězce.
- Vytváření řetězců je bez přidělení kromě paměť používanou řetězec (s výjimkou `ToArray()` volání).

## <a name="adapters"></a>Adaptéry

Teď, když oba `Body` a `BodyPipe` vlastnosti jsou k dispozici pro `HttpRequest` a `HttpResponse`, co se stane, když nastavíte `Body` na jiný datový proud? Nová sada adaptérů 3.0, automaticky přizpůsobí každého typu na druhý. Například pokud nastavíte `HttpRequest.Body` do nového datového proudu `HttpRequest.BodyPipe` se automaticky nastaví na novou `PipeReader` tím končí naše `HttpRequest.Body`. Stejné chování platí pro nastavení `BodyPipe` vlastnost. Pokud `HttpResponse.BodyPipe` je nastavena na novou `PipeWriter`, `HttpResponse.Body` se automaticky nastaví na nový datový proud, který obtéká `HttpResponse.BodyPipe`.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` Novinky v 3.0. Používá se k označení, že jsou hlavičky neupravitelných a ke spuštění `OnStarting` zpětná volání. V 3.0 preview3, je nutné volat `StartAsync` před použitím `HttpRequest.BodyPipe`, a v budoucích verzích bude doporučení. Používáte-li Kestrel jako serveru, volání StartAsync před použitím `PipeReader` zaručuje, že paměť vrácený `GetMemory` bude patřit Kestrel vaší interní <xref:System.IO.Pipelines.Pipe> místo externí vyrovnávací paměti.

## <a name="additional-resources"></a>Další zdroje

- [Představujeme System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>