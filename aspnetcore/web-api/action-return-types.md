---
title: Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API
author: scottaddie
description: Přečtěte si o použití různých návratových typů metody kontroleru v ASP.NET Core webovém rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: 79134ab252f309f8b39b8db5f8f3e82035e0eb7f
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815748"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API

[Scott Addie](https://github.com/scottaddie)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([stažení](xref:index#how-to-download-a-sample))

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

Nejjednodušší akce vrátí primitivní nebo komplexní datový typ (například `string` nebo vlastní typ objektu). Vezměte v úvahu následující akci, která vrátí kolekci vlastních `Product` objektů:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Bez známých podmínek pro ochranu proti během provádění akce může být pro vrácení určitého typu stačit. Předchozí akce nepřijímá žádné parametry, takže ověření omezení parametrů není nutné.

Pokud je nutné, aby byly v akci účtovány známé podmínky, jsou zavedeny vícenásobné návratové cesty. V takovém případě je běžné kombinovat <xref:Microsoft.AspNetCore.Mvc.ActionResult> návratový typ s primitivním nebo komplexním návratovým typem. K tomuto typu akce musí > [IActionResult](#iactionresult-type) nebo [ActionResult\<T](#actionresultt-type) .

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Vrátit IEnumerable\<t > nebo IAsyncEnumerable\<T >

V ASP.NET Core 2,2 a starších verzích vrátila <xref:System.Collections.Generic.IAsyncEnumerable%601> akce výsledkem synchronní iterace kolekce serializátorem. Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání. K ilustraci si představte, že jádro Entity Framework (EF) se používá pro potřeby přístupu k datům webového rozhraní API. Následující návratový typ akce je synchronně výčtu během serializace:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Aby se zabránilo synchronnímu výčtu a blokování čekání na databázi v ASP.NET Core 2,2 a starších verzích `ToListAsync`, vyvolejte:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

V ASP.NET Core 3,0 a novějším se `IAsyncEnumerable<T>` vrátí akce:

* Již nevede k synchronní iteraci.
* Se bude co nejefektivnější <xref:System.Collections.Generic.IEnumerable%601>jako vrácení.

ASP.NET Core 3,0 a novější vyrovnávací paměti výsledek následující akce před jejím poskytnutím serializátoru:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Zvažte deklaraci návratového typu podpisu akce, `IAsyncEnumerable<T>` aby se zajistila asynchronní iterace. V konečném případě je režim iterace založen na vráceném základním konkrétním typu. MVC automaticky ukládá do vyrovnávací paměti jakýkoli konkrétní typ `IAsyncEnumerable<T>`, který implementuje.

Vezměte v úvahu následující akci, která vrací záznamy produktů s cenami za `IEnumerable<Product>`prodej jako:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>` Ekvivalent předchozí akce:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Obě předchozí akce nejsou blokující ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Typ IActionResult

Návratový typ je vhodný, pokud je `ActionResult` možné v akci použít více návratových typů. <xref:Microsoft.AspNetCore.Mvc.IActionResult> `ActionResult` Typy reprezentují různé stavové kódy HTTP. Jakákoli Neabstraktní třída odvozená od `ActionResult` je platná jako platný návratový typ. Některé běžné návratové typy v této kategorii <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> jsou (400) <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> , (404) a <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternativně lze metody ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídě použít k vrácení `ActionResult` typů z akce. Například `return BadRequest();` je zkrácený `return new BadRequestResult();`tvar.

Vzhledem k tomu, že v tomto typu akce existuje více návratových typů a cest, je nezbytné použít atribut [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) . Tento atribut vytváří podrobnější podrobnosti odpovědi na stránky pro nápovědu k webovému rozhraní API generované nástroji, jako je [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]`Určuje známé typy a stavové kódy HTTP, které má akce vrátit.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte následující synchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

V předchozí akci:

* Pokud produkt reprezentovaný `id` neexistují v podkladovém úložišti dat, vrátí se stavový kód 404. Metoda usnadnění je vyvolána jako zkrácený pro `return new NotFoundResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>
* Stavový kód 200 se vrátí s `Product` objektem, když produkt existuje. Metoda usnadnění je vyvolána jako zkrácený pro `return new OkObjectResult(product);`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte následující asynchronní akci, ve které existují dva možné návratové typy:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

::: moniker-end

V předchozí akci:

* Pokud popis produktu obsahuje "widget XYZ", vrátí se stavový kód 400. Metoda usnadnění je vyvolána jako zkrácený pro `return new BadRequestResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>
* Kód stavu 201 je vygenerován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou pohodlí při vytvoření produktu. Alternativa k volání `CreatedAtAction` je `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. V této cestě `Product` kódu je objekt uveden v těle odpovědi. Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu. `Location`

Například následující model označuje, že požadavky musí zahrnovat `Name` vlastnosti a. `Description` Nepovedlo `Name` se `Description` poskytnout a v žádosti dojde k selhání ověřování modelu.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Pokud je použit atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) v ASP.NET Core 2,1 nebo novějším, výsledkem chyb ověření modelu je 400 stavového kódu. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>ActionResult\<T – typ >

ASP.NET Core 2,1 zavedla pro akce kontroleru webového rozhraní API > návratový typ [ActionResult\<T](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) . Umožňuje vrátit typ odvozený z <xref:Microsoft.AspNetCore.Mvc.ActionResult> nebo vracet [konkrétní typ](#specific-type). `ActionResult<T>`nabízí následující výhody oproti [IActionResult typu](#iactionresult-type):

* `Type` Vlastnost atributu [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) lze vyloučit. Například `[ProducesResponseType(200, Type = typeof(Product))]` je zjednodušený pro `[ProducesResponseType(200)]`. Očekávaný návratový typ akce je místo odvoditelné z `T` v. `ActionResult<T>`
* [Operátory implicitního přetypování](/dotnet/csharp/language-reference/keywords/implicit) podporují převod obou `T` `ActionResult` i na `ActionResult<T>`. `T`převádí na <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, což znamená `return new ObjectResult(T);` , že je `return T;`zjednodušený.

C#nepodporuje implicitní operátory přetypování na rozhraních. V důsledku toho je nutné použít `ActionResult<T>`převod rozhraní na konkrétní typ. Například použití `IEnumerable` v následujícím příkladu nefunguje:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Jednou z možností, jak opravit předchozí kód, je `_repository.GetProducts().ToList();`vrátit.

Většina akcí má určitý návratový typ. Během provádění akce mohou nastat neočekávané podmínky. v takovém případě se konkrétní typ nevrátí. Například vstupní parametr akce může selhat při ověřování modelu. V takovém případě je běžné vracet odpovídající `ActionResult` typ místo konkrétního typu.

### <a name="synchronous-action"></a>Synchronní akce

Zvažte synchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=7,10)]

V předchozí akci:

* Stavový kód 404 se vrátí, když produkt v databázi neexistuje.
* Pokud produkt existuje, vrátí se stavový kód `Product` 200 s odpovídajícím objektem. Před ASP.NET Core 2,1 `return product;` musí být `return Ok(product);`řádek.

### <a name="asynchronous-action"></a>Asynchronní akce

Zvažte asynchronní akci, ve které existují dva možné návratové typy:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

V předchozí akci:

* ASP.NET Core Runtime vrátí stavový<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>kód 400, když:
  * Atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) byl použit a ověření modelu se nezdařilo.
  * Popis produktu obsahuje "widget XYZ".
* Kód stavu 201 je generován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou při vytvoření produktu. V této cestě `Product` kódu je objekt uveden v těle odpovědi. Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu. `Location`

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
