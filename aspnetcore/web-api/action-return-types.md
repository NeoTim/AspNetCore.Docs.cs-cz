---
title: Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API
author: scottaddie
description: Přečtěte si o použití různých návratových typů metody kontroleru v ASP.NET Core webovém rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/action-return-types
ms.openlocfilehash: 3058fabb0c08ac62956c18f3c294692d35122e12
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022157"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API

[Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

ASP.NET Core nabízí následující možnosti pro návratové typy akce řadiče webového rozhraní API:

::: moniker range=">= aspnetcore-2.1"

* [Konkrétní typ](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Konkrétní typ](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Tento dokument vysvětluje, kdy je nejvhodnější použít každý návratový typ.

## <a name="specific-type"></a>Konkrétní typ

Nejjednodušší akce vrátí primitivní nebo komplexní datový typ (například `string` nebo vlastní typ objektu). Vezměte v úvahu následující akci, která vrátí kolekci vlastních `Product` objektů:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Bez známých podmínek pro ochranu proti během provádění akce může být pro vrácení určitého typu stačit. Předchozí akce nepřijímá žádné parametry, takže ověření omezení parametrů není nutné.

Pokud je možné více návratových typů, je běžné kombinovat <xref:Microsoft.AspNetCore.Mvc.ActionResult> návratový typ s primitivním nebo komplexním návratovým typem. K tomuto typu akce musí být [IActionResult](#iactionresult-type) nebo [ActionResult \<T> ](#actionresultt-type) . V tomto dokumentu je uvedeno několik ukázek více návratových typů.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Vrátit IEnumerable \<T> nebo IAsyncEnumerable\<T>

V ASP.NET Core 2,2 a starších verzích vrátila <xref:System.Collections.Generic.IEnumerable%601> Akce výsledkem synchronní iterace kolekce serializátorem. Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání. K ilustraci si představte, že jádro Entity Framework (EF) se používá pro potřeby přístupu k datům webového rozhraní API. Následující návratový typ akce je synchronně výčtu během serializace:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Aby se zabránilo synchronnímu výčtu a blokování čekání na databázi v ASP.NET Core 2,2 a starších verzích, vyvolejte `ToListAsync` :

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

V ASP.NET Core 3,0 a novějším se vrátí `IAsyncEnumerable<T>` akce:

* Již nevede k synchronní iteraci.
* Se bude co nejefektivnější jako vrácení <xref:System.Collections.Generic.IEnumerable%601> .

ASP.NET Core 3,0 a novější vyrovnávací paměti výsledek následující akce před jejím poskytnutím serializátoru:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Zvažte deklaraci návratového typu podpisu akce, `IAsyncEnumerable<T>` aby se zajistila asynchronní iterace. V konečném případě je režim iterace založen na vráceném základním konkrétním typu. MVC automaticky ukládá do vyrovnávací paměti jakýkoli konkrétní typ, který implementuje `IAsyncEnumerable<T>` .

Vezměte v úvahu následující akci, která vrací záznamy produktů s cenami za prodej jako `IEnumerable<Product>` :

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>`Ekvivalent předchozí akce:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Obě předchozí akce nejsou blokující ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Typ IActionResult

<xref:Microsoft.AspNetCore.Mvc.IActionResult>Návratový typ je vhodný, pokud `ActionResult` je možné v akci použít více návratových typů. `ActionResult`Typy reprezentují různé stavové kódy HTTP. Jakákoli Neabstraktní třída odvozená od `ActionResult` je platná jako platný návratový typ. Některé běžné návratové typy v této kategorii jsou <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) a <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternativně lze metody ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídě použít k vrácení `ActionResult` typů z akce. Například `return BadRequest();` je zkrácený tvar `return new BadRequestResult();` .

Vzhledem k tomu, že v tomto typu akce existuje více návratových typů a cest, je nutné použít atribut k dispozici [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) . Tento atribut vytváří podrobnější podrobnosti odpovědi na stránky pro nápovědu k webovému rozhraní API generované nástroji, jako je [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]`Určuje známé typy a stavové kódy HTTP, které má akce vrátit.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte následující synchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

V předchozí akci:

* Pokud produkt reprezentovaný `id` neexistují v podkladovém úložišti dat, vrátí se stavový kód 404. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Metoda usnadnění je vyvolána jako zkrácený pro `return new NotFoundResult();` .
* Stavový kód 200 se vrátí s `Product` objektem, když produkt existuje. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Metoda usnadnění je vyvolána jako zkrácený pro `return new OkObjectResult(product);` .

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte následující asynchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

V předchozí akci:

* Pokud popis produktu obsahuje "widget XYZ", vrátí se stavový kód 400. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Metoda usnadnění je vyvolána jako zkrácený pro `return new BadRequestResult();` .
* Kód stavu 201 je vygenerován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou pohodlí při vytvoření produktu. Alternativa k volání `CreatedAtAction` je `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` . V této cestě kódu `Product` je objekt uveden v těle odpovědi. `Location`Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu.

Například následující model označuje, že požadavky musí zahrnovat `Name` `Description` vlastnosti a. Nepovedlo se poskytnout `Name` a `Description` v žádosti dojde k selhání ověřování modelu.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Pokud [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) je použit atribut v ASP.NET Core 2,1 nebo novějším, výsledkem chyb ověření modelu je kód stavu 400. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>\<T>Typ ActionResult

ASP.NET Core 2,1 zavedlo návratový typ [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) pro akce kontroleru webového rozhraní API. Umožňuje vrátit typ odvozený z <xref:Microsoft.AspNetCore.Mvc.ActionResult> nebo vracet [konkrétní typ](#specific-type). `ActionResult<T>`nabízí následující výhody oproti [IActionResult typu](#iactionresult-type):

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Vlastnost atributu může být vyloučena. Například `[ProducesResponseType(200, Type = typeof(Product))]` je zjednodušený pro `[ProducesResponseType(200)]` . Očekávaný návratový typ akce je místo odvoditelné z `T` v `ActionResult<T>` .
* [Operátory implicitního přetypování](/dotnet/csharp/language-reference/keywords/implicit) podporují převod obou `T` i `ActionResult` na `ActionResult<T>` . `T`převádí na <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , což znamená, že `return new ObjectResult(T);` je zjednodušený `return T;` .

Jazyk C# nepodporuje operátory implicitního přetypování na rozhraních. V důsledku toho je nutné použít převod rozhraní na konkrétní typ `ActionResult<T>` . Například použití `IEnumerable` v následujícím příkladu nefunguje:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Jednou z možností, jak opravit předchozí kód, je vrátit `_repository.GetProducts().ToList();` .

Většina akcí má určitý návratový typ. Během provádění akce mohou nastat neočekávané podmínky. v takovém případě se konkrétní typ nevrátí. Například vstupní parametr akce může selhat při ověřování modelu. V takovém případě je běžné vracet odpovídající `ActionResult` typ místo konkrétního typu.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte synchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

V předchozí akci:

* Stavový kód 404 se vrátí, když produkt v databázi neexistuje.
* Pokud produkt existuje, vrátí se stavový kód 200 s odpovídajícím `Product` objektem. Před ASP.NET Core 2,1 musí `return product;` být řádek `return Ok(product);` .

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte asynchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

V předchozí akci:

* ASP.NET Core Runtime vrátí stavový kód 400 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> , když:
  * [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Atribut byl použit a ověření modelu se nezdařilo.
  * Popis produktu obsahuje "widget XYZ".
* Kód stavu 201 je generován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou při vytvoření produktu. V této cestě kódu `Product` je objekt uveden v těle odpovědi. `Location`Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu.

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
