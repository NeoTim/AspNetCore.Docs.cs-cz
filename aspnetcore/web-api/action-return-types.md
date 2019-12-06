---
title: Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API
author: scottaddie
description: Přečtěte si o použití různých návratových typů metody kontroleru v ASP.NET Core webovém rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: fe665026fdced22ccf4b4f1ba655e858a7acf016
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879744"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="4f51e-103">Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API</span><span class="sxs-lookup"><span data-stu-id="4f51e-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="4f51e-104">[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="4f51e-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="4f51e-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4f51e-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4f51e-106">ASP.NET Core nabízí následující možnosti pro návratové typy akce řadiče webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4f51e-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="4f51e-107">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="4f51e-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="4f51e-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="4f51e-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="4f51e-109">ActionResult\<T ></span><span class="sxs-lookup"><span data-stu-id="4f51e-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="4f51e-110">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="4f51e-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="4f51e-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="4f51e-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="4f51e-112">Tento dokument vysvětluje, kdy je nejvhodnější použít každý návratový typ.</span><span class="sxs-lookup"><span data-stu-id="4f51e-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="4f51e-113">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="4f51e-113">Specific type</span></span>

<span data-ttu-id="4f51e-114">Nejjednodušší akce vrátí primitivní nebo komplexní datový typ (například `string` nebo vlastní typ objektu).</span><span class="sxs-lookup"><span data-stu-id="4f51e-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="4f51e-115">Vezměte v úvahu následující akci, která vrátí kolekci vlastních objektů `Product`:</span><span class="sxs-lookup"><span data-stu-id="4f51e-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="4f51e-116">Bez známých podmínek pro ochranu proti během provádění akce může být pro vrácení určitého typu stačit.</span><span class="sxs-lookup"><span data-stu-id="4f51e-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="4f51e-117">Předchozí akce nepřijímá žádné parametry, takže ověření omezení parametrů není nutné.</span><span class="sxs-lookup"><span data-stu-id="4f51e-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="4f51e-118">Pokud je nutné, aby byly v akci účtovány známé podmínky, jsou zavedeny vícenásobné návratové cesty.</span><span class="sxs-lookup"><span data-stu-id="4f51e-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="4f51e-119">V takovém případě je běžné zamíchat <xref:Microsoft.AspNetCore.Mvc.ActionResult> návratový typ pomocí primitivního nebo komplexního návratového typu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-119">In such a case, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="4f51e-120">Pro tento typ akce musí být >\<[IActionResult](#iactionresult-type) nebo [ActionResult t](#actionresultt-type) .</span><span class="sxs-lookup"><span data-stu-id="4f51e-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="4f51e-121">Vrátit IEnumerable\<T > nebo IAsyncEnumerable\<T ></span><span class="sxs-lookup"><span data-stu-id="4f51e-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="4f51e-122">V ASP.NET Core 2,2 a starších verzích vrátila <xref:System.Collections.Generic.IEnumerable%601> z akce výsledkem synchronní iterace sběru serializátoru.</span><span class="sxs-lookup"><span data-stu-id="4f51e-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="4f51e-123">Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání.</span><span class="sxs-lookup"><span data-stu-id="4f51e-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="4f51e-124">K ilustraci si představte, že jádro Entity Framework (EF) se používá pro potřeby přístupu k datům webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4f51e-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="4f51e-125">Následující návratový typ akce je synchronně výčtu během serializace:</span><span class="sxs-lookup"><span data-stu-id="4f51e-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="4f51e-126">Aby se zabránilo synchronnímu výčtu a blokování čekání na databázi v ASP.NET Core 2,2 a starších verzích, volejte `ToListAsync`:</span><span class="sxs-lookup"><span data-stu-id="4f51e-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="4f51e-127">V ASP.NET Core 3,0 a novějším vrátí `IAsyncEnumerable<T>` z akce:</span><span class="sxs-lookup"><span data-stu-id="4f51e-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="4f51e-128">Již nevede k synchronní iteraci.</span><span class="sxs-lookup"><span data-stu-id="4f51e-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="4f51e-129">Se bude nacházet stejně efektivně jako vrácení <xref:System.Collections.Generic.IEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="4f51e-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="4f51e-130">ASP.NET Core 3,0 a novější vyrovnávací paměti výsledek následující akce před jejím poskytnutím serializátoru:</span><span class="sxs-lookup"><span data-stu-id="4f51e-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="4f51e-131">Zvažte deklaraci návratového typu podpisu akce jako `IAsyncEnumerable<T>` pro zaručení asynchronní iterace.</span><span class="sxs-lookup"><span data-stu-id="4f51e-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="4f51e-132">V konečném případě je režim iterace založen na vráceném základním konkrétním typu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="4f51e-133">MVC automaticky ukládá do vyrovnávací paměti jakýkoli konkrétní typ, který implementuje `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="4f51e-134">Vezměte v úvahu následující akci, která vrací záznamy produktů s cenami za prodej jako `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="4f51e-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="4f51e-135">`IAsyncEnumerable<Product>` ekvivalent předchozí akce:</span><span class="sxs-lookup"><span data-stu-id="4f51e-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="4f51e-136">Obě předchozí akce nejsou blokující ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="4f51e-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="4f51e-137">Typ IActionResult</span><span class="sxs-lookup"><span data-stu-id="4f51e-137">IActionResult type</span></span>

<span data-ttu-id="4f51e-138">Návratový typ <xref:Microsoft.AspNetCore.Mvc.IActionResult> je vhodný, pokud je možné v akci použít více `ActionResult` návratových typů.</span><span class="sxs-lookup"><span data-stu-id="4f51e-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="4f51e-139">Typy `ActionResult` reprezentují různé kódy stavu HTTP.</span><span class="sxs-lookup"><span data-stu-id="4f51e-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="4f51e-140">Jakákoli Neabstraktní třída odvozená od `ActionResult` je vyhodnocena jako platný návratový typ.</span><span class="sxs-lookup"><span data-stu-id="4f51e-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="4f51e-141">Některé běžné návratové typy v této kategorii jsou <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) a <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="4f51e-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="4f51e-142">Alternativně lze snadno použít metody v <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídy k vrácení `ActionResult`ch typů z akce.</span><span class="sxs-lookup"><span data-stu-id="4f51e-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="4f51e-143">`return BadRequest();` je například zkrácený tvar `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="4f51e-144">Vzhledem k tomu, že v tomto typu akce existuje více návratových typů a cest, je nutné použít atribut [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="4f51e-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="4f51e-145">Tento atribut vytváří podrobnější podrobnosti odpovědi na stránky pro nápovědu k webovému rozhraní API generované nástroji, jako je [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="4f51e-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="4f51e-146">`[ProducesResponseType]` označuje známé typy a stavové kódy HTTP, které má akce vrátit.</span><span class="sxs-lookup"><span data-stu-id="4f51e-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="4f51e-147">Synchronní akce</span><span class="sxs-lookup"><span data-stu-id="4f51e-147">Synchronous action</span></span>

<span data-ttu-id="4f51e-148">Zvažte následující synchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="4f51e-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="4f51e-149">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="4f51e-149">In the preceding action:</span></span>

* <span data-ttu-id="4f51e-150">Stavový kód 404 se vrátí, pokud produkt reprezentovaný `id` neexistuje v podkladovém úložišti dat.</span><span class="sxs-lookup"><span data-stu-id="4f51e-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="4f51e-151">Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> pohodlí je vyvolán jako zkrácený pro `return new NotFoundResult();`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="4f51e-152">Pokud produkt existuje, vrátí se stavový kód 200 s objektem `Product`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="4f51e-153">Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> pohodlí je vyvolán jako zkrácený pro `return new OkObjectResult(product);`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="4f51e-154">Asynchronní akce</span><span class="sxs-lookup"><span data-stu-id="4f51e-154">Asynchronous action</span></span>

<span data-ttu-id="4f51e-155">Zvažte následující asynchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="4f51e-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="4f51e-156">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="4f51e-156">In the preceding action:</span></span>

* <span data-ttu-id="4f51e-157">Pokud popis produktu obsahuje "widget XYZ", vrátí se stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="4f51e-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="4f51e-158">Způsob <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> pohodlí je vyvolán jako zkrácený pro `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="4f51e-159">Kód stavu 201 je generován metodou <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> pohodlí při vytvoření produktu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="4f51e-160">Alternativa k volání `CreatedAtAction` je `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="4f51e-161">V této cestě kódu je objekt `Product` k dispozici v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4f51e-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="4f51e-162">Poskytla se hlavička odpovědi `Location` obsahující nově vytvořenou adresu URL produktu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="4f51e-163">Například následující model označuje, že požadavky musí zahrnovat vlastnosti `Name` a `Description`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="4f51e-164">Nepovedlo se poskytnout `Name` a `Description` v žádosti způsobí, že se ověření modelu nezdaří.</span><span class="sxs-lookup"><span data-stu-id="4f51e-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4f51e-165">Pokud je použit atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) v ASP.NET Core 2,1 nebo novějším, chyby ověření modelu budou mít za následek stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="4f51e-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="4f51e-166">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="4f51e-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="4f51e-167">Typ > ActionResult\<T</span><span class="sxs-lookup"><span data-stu-id="4f51e-167">ActionResult\<T> type</span></span>

<span data-ttu-id="4f51e-168">ASP.NET Core 2,1 zavedla > návratový typ [ActionResult\<t](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) pro akce kontroleru webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4f51e-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="4f51e-169">Umožňuje vracet typ odvozený od <xref:Microsoft.AspNetCore.Mvc.ActionResult> nebo vracet [konkrétní typ](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="4f51e-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="4f51e-170">`ActionResult<T>` nabízí následující výhody oproti [IActionResult typu](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="4f51e-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="4f51e-171">Vlastnost `Type` atributu [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) lze vyloučit.</span><span class="sxs-lookup"><span data-stu-id="4f51e-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="4f51e-172">Například `[ProducesResponseType(200, Type = typeof(Product))]` je zjednodušený pro `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="4f51e-173">Očekávaný návratový typ akce je místo toho odvozen z `T` v `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="4f51e-174">[Operátory implicitního přetypování](/dotnet/csharp/language-reference/keywords/implicit) podporují převod `T` a `ActionResult` na `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="4f51e-175">`T` se převede na <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, což znamená, že `return new ObjectResult(T);` je zjednodušená a `return T;`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="4f51e-176">C#nepodporuje implicitní operátory přetypování na rozhraních.</span><span class="sxs-lookup"><span data-stu-id="4f51e-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="4f51e-177">V důsledku toho je nutné převod rozhraní na konkrétní typ použít `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="4f51e-178">Například použití `IEnumerable` v následujícím příkladu nefunguje:</span><span class="sxs-lookup"><span data-stu-id="4f51e-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="4f51e-179">Jednou z možností, jak předchozí kód opravit, je vrácení `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="4f51e-180">Většina akcí má určitý návratový typ.</span><span class="sxs-lookup"><span data-stu-id="4f51e-180">Most actions have a specific return type.</span></span> <span data-ttu-id="4f51e-181">Během provádění akce mohou nastat neočekávané podmínky. v takovém případě se konkrétní typ nevrátí.</span><span class="sxs-lookup"><span data-stu-id="4f51e-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="4f51e-182">Například vstupní parametr akce může selhat při ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="4f51e-183">V takovém případě je běžné místo konkrétního typu vracet odpovídající typ `ActionResult`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="4f51e-184">Synchronní akce</span><span class="sxs-lookup"><span data-stu-id="4f51e-184">Synchronous action</span></span>

<span data-ttu-id="4f51e-185">Zvažte synchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="4f51e-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="4f51e-186">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="4f51e-186">In the preceding action:</span></span>

* <span data-ttu-id="4f51e-187">Stavový kód 404 se vrátí, když produkt v databázi neexistuje.</span><span class="sxs-lookup"><span data-stu-id="4f51e-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="4f51e-188">Pokud produkt existuje, vrátí se stavový kód 200 s odpovídajícím objektem `Product`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="4f51e-189">Před ASP.NET Core 2,1 musí být `return product;` řádek `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="4f51e-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="4f51e-190">Asynchronní akce</span><span class="sxs-lookup"><span data-stu-id="4f51e-190">Asynchronous action</span></span>

<span data-ttu-id="4f51e-191">Zvažte asynchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="4f51e-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="4f51e-192">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="4f51e-192">In the preceding action:</span></span>

* <span data-ttu-id="4f51e-193">ASP.NET Core Runtime vrací kód stavu 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="4f51e-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="4f51e-194">Byl použit atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) a ověření modelu se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="4f51e-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="4f51e-195">Popis produktu obsahuje "widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="4f51e-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="4f51e-196">Stavový kód 201 je při vytvoření produktu generován metodou <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>.</span><span class="sxs-lookup"><span data-stu-id="4f51e-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="4f51e-197">V této cestě kódu je objekt `Product` k dispozici v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4f51e-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="4f51e-198">Poskytla se hlavička odpovědi `Location` obsahující nově vytvořenou adresu URL produktu.</span><span class="sxs-lookup"><span data-stu-id="4f51e-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4f51e-199">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4f51e-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
