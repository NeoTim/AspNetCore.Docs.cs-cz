---
title: Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API
author: scottaddie
description: Přečtěte si o použití různých návratových typů metody kontroleru v ASP.NET Core webovém rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: c409170a24225e160c1c53e7294590589e114f7f
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116081"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API

[Scott Addie](https://github.com/scottaddie)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

ASP.NET Core nabízí následující možnosti pro návratové typy akce řadiče webového rozhraní API:

::: moniker range=">= aspnetcore-2.1"

* [Konkrétní typ](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T >](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Konkrétní typ](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Tento dokument vysvětluje, kdy je nejvhodnější použít každý návratový typ.

## <a name="specific-type"></a>Konkrétní typ

Nejjednodušší akce vrátí primitivní nebo komplexní datový typ (například `string` nebo vlastní typ objektu). Vezměte v úvahu následující akci, která vrátí kolekci vlastních objektů `Product`:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Bez známých podmínek pro ochranu proti během provádění akce může být pro vrácení určitého typu stačit. Předchozí akce nepřijímá žádné parametry, takže ověření omezení parametrů není nutné.

Pokud je nutné, aby byly v akci účtovány známé podmínky, jsou zavedeny vícenásobné návratové cesty. V takovém případě je běžné zamíchat <xref:Microsoft.AspNetCore.Mvc.ActionResult> návratový typ pomocí primitivního nebo komplexního návratového typu. Pro tento typ akce musí být >\<[IActionResult](#iactionresult-type) nebo [ActionResult t](#actionresultt-type) .

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Vrátit IEnumerable\<T > nebo IAsyncEnumerable\<T >

V ASP.NET Core 2,2 a starších verzích vrátila <xref:System.Collections.Generic.IEnumerable%601> z akce výsledkem synchronní iterace sběru serializátoru. Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání. K ilustraci si představte, že jádro Entity Framework (EF) se používá pro potřeby přístupu k datům webového rozhraní API. Následující návratový typ akce je synchronně výčtu během serializace:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Aby se zabránilo synchronnímu výčtu a blokování čekání na databázi v ASP.NET Core 2,2 a starších verzích, volejte `ToListAsync`:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

V ASP.NET Core 3,0 a novějším vrátí `IAsyncEnumerable<T>` z akce:

* Již nevede k synchronní iteraci.
* Se bude nacházet stejně efektivně jako vrácení <xref:System.Collections.Generic.IEnumerable%601>.

ASP.NET Core 3,0 a novější vyrovnávací paměti výsledek následující akce před jejím poskytnutím serializátoru:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Zvažte deklaraci návratového typu podpisu akce jako `IAsyncEnumerable<T>` pro zaručení asynchronní iterace. V konečném případě je režim iterace založen na vráceném základním konkrétním typu. MVC automaticky ukládá do vyrovnávací paměti jakýkoli konkrétní typ, který implementuje `IAsyncEnumerable<T>`.

Vezměte v úvahu následující akci, která vrací záznamy produktů s cenami za prodej jako `IEnumerable<Product>`:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>` ekvivalent předchozí akce:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Obě předchozí akce nejsou blokující ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Typ IActionResult

Návratový typ <xref:Microsoft.AspNetCore.Mvc.IActionResult> je vhodný, pokud je možné v akci použít více `ActionResult` návratových typů. Typy `ActionResult` reprezentují různé kódy stavu HTTP. Jakákoli Neabstraktní třída odvozená od `ActionResult` je vyhodnocena jako platný návratový typ. Některé běžné návratové typy v této kategorii jsou <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) a <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternativně lze snadno použít metody v <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídy k vrácení `ActionResult`ch typů z akce. `return BadRequest();` je například zkrácený tvar `return new BadRequestResult();`.

Vzhledem k tomu, že v tomto typu akce existuje více návratových typů a cest, je nezbytné použít atribut [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) . Tento atribut vytváří podrobnější podrobnosti odpovědi na stránky pro nápovědu k webovému rozhraní API generované nástroji, jako je [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` označuje známé typy a stavové kódy HTTP, které má akce vrátit.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte následující synchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

V předchozí akci:

* Stavový kód 404 se vrátí, pokud produkt reprezentovaný `id` neexistuje v podkladovém úložišti dat. Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> pohodlí je vyvolán jako zkrácený pro `return new NotFoundResult();`.
* Pokud produkt existuje, vrátí se stavový kód 200 s objektem `Product`. Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> pohodlí je vyvolán jako zkrácený pro `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte následující asynchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

V předchozí akci:

* Pokud popis produktu obsahuje "widget XYZ", vrátí se stavový kód 400. Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pohodlí je vyvolán jako zkrácený pro `return new BadRequestResult();`.
* Kód stavu 201 je generován metodou <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> pohodlí při vytvoření produktu. Alternativa k volání `CreatedAtAction` je `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. V této cestě kódu je objekt `Product` k dispozici v těle odpovědi. Poskytla se hlavička odpovědi `Location` obsahující nově vytvořenou adresu URL produktu.

Například následující model označuje, že požadavky musí zahrnovat vlastnosti `Name` a `Description`. Nepovedlo se poskytnout `Name` a `Description` v žádosti způsobí, že se ověření modelu nezdaří.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Pokud je použit atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) v ASP.NET Core 2,1 nebo novějším, výsledkem chyb ověření modelu je 400 stavového kódu. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Typ > ActionResult\<T

ASP.NET Core 2,1 zavedla > návratový typ [ActionResult\<t](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) pro akce kontroleru webového rozhraní API. Umožňuje vracet typ odvozený od <xref:Microsoft.AspNetCore.Mvc.ActionResult> nebo vracet [konkrétní typ](#specific-type). `ActionResult<T>` nabízí následující výhody oproti [IActionResult typu](#iactionresult-type):

* Vlastnost `Type` atributu [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) lze vyloučit. Například `[ProducesResponseType(200, Type = typeof(Product))]` je zjednodušený pro `[ProducesResponseType(200)]`. Očekávaný návratový typ akce je místo toho odvozen z `T` v `ActionResult<T>`.
* [Operátory implicitního přetypování](/dotnet/csharp/language-reference/keywords/implicit) podporují převod `T` a `ActionResult` na `ActionResult<T>`. `T` se převede na <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, což znamená, že `return new ObjectResult(T);` je zjednodušená a `return T;`.

C#nepodporuje implicitní operátory přetypování na rozhraních. V důsledku toho je nutné převod rozhraní na konkrétní typ použít `ActionResult<T>`. Například použití `IEnumerable` v následujícím příkladu nefunguje:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Jednou z možností, jak předchozí kód opravit, je vrácení `_repository.GetProducts().ToList();`.

Většina akcí má určitý návratový typ. Během provádění akce mohou nastat neočekávané podmínky. v takovém případě se konkrétní typ nevrátí. Například vstupní parametr akce může selhat při ověřování modelu. V takovém případě je běžné místo konkrétního typu vracet odpovídající typ `ActionResult`.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte synchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

V předchozí akci:

* Stavový kód 404 se vrátí, když produkt v databázi neexistuje.
* Pokud produkt existuje, vrátí se stavový kód 200 s odpovídajícím objektem `Product`. Před ASP.NET Core 2,1 musí být `return product;` řádek `return Ok(product);`.

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte asynchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

V předchozí akci:

* ASP.NET Core Runtime vrací kód stavu 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) v těchto případech:
  * Atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) byl použit a ověření modelu se nezdařilo.
  * Popis produktu obsahuje "widget XYZ".
* Stavový kód 201 je při vytvoření produktu generován metodou <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>. V této cestě kódu je objekt `Product` k dispozici v těle odpovědi. Poskytla se hlavička odpovědi `Location` obsahující nově vytvořenou adresu URL produktu.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
