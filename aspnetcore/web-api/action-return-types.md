---
title: Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API
author: scottaddie
description: Přečtěte si o použití různých návratových typů metody kontroleru v ASP.NET Core webovém rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: 991265810324d6339ebf346ff9aa14c479112af9
ms.sourcegitcommit: fa61d882be9d0c48bd681f2efcb97e05522051d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71205767"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="55b0c-103">Návratové typy akcí kontroleru v ASP.NET Core webovém rozhraní API</span><span class="sxs-lookup"><span data-stu-id="55b0c-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="55b0c-104">[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="55b0c-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="55b0c-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55b0c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="55b0c-106">ASP.NET Core nabízí následující možnosti pro návratové typy akce řadiče webového rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="55b0c-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="55b0c-107">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="55b0c-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="55b0c-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="55b0c-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="55b0c-109">ActionResult\<T ></span><span class="sxs-lookup"><span data-stu-id="55b0c-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="55b0c-110">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="55b0c-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="55b0c-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="55b0c-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="55b0c-112">Tento dokument vysvětluje, kdy je nejvhodnější použít každý návratový typ.</span><span class="sxs-lookup"><span data-stu-id="55b0c-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="55b0c-113">Konkrétní typ</span><span class="sxs-lookup"><span data-stu-id="55b0c-113">Specific type</span></span>

<span data-ttu-id="55b0c-114">Nejjednodušší akce vrátí primitivní nebo komplexní datový typ (například `string` nebo vlastní typ objektu).</span><span class="sxs-lookup"><span data-stu-id="55b0c-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="55b0c-115">Vezměte v úvahu následující akci, která vrátí kolekci vlastních `Product` objektů:</span><span class="sxs-lookup"><span data-stu-id="55b0c-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="55b0c-116">Bez známých podmínek pro ochranu proti během provádění akce může být pro vrácení určitého typu stačit.</span><span class="sxs-lookup"><span data-stu-id="55b0c-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="55b0c-117">Předchozí akce nepřijímá žádné parametry, takže ověření omezení parametrů není nutné.</span><span class="sxs-lookup"><span data-stu-id="55b0c-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="55b0c-118">Pokud je nutné, aby byly v akci účtovány známé podmínky, jsou zavedeny vícenásobné návratové cesty.</span><span class="sxs-lookup"><span data-stu-id="55b0c-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="55b0c-119">V takovém případě je běžné kombinovat <xref:Microsoft.AspNetCore.Mvc.ActionResult> návratový typ s primitivním nebo komplexním návratovým typem.</span><span class="sxs-lookup"><span data-stu-id="55b0c-119">In such a case, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="55b0c-120">K tomuto typu akce musí > [IActionResult](#iactionresult-type) nebo [ActionResult\<T](#actionresultt-type) .</span><span class="sxs-lookup"><span data-stu-id="55b0c-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="55b0c-121">Vrátit IEnumerable\<t > nebo IAsyncEnumerable\<T ></span><span class="sxs-lookup"><span data-stu-id="55b0c-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="55b0c-122">V ASP.NET Core 2,2 a starších verzích vrátila <xref:System.Collections.Generic.IAsyncEnumerable%601> akce výsledkem synchronní iterace kolekce serializátorem.</span><span class="sxs-lookup"><span data-stu-id="55b0c-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IAsyncEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="55b0c-123">Výsledkem je blokování volání a potenciál pro fond vláken vyčerpání.</span><span class="sxs-lookup"><span data-stu-id="55b0c-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="55b0c-124">K ilustraci si představte, že jádro Entity Framework (EF) se používá pro potřeby přístupu k datům webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="55b0c-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="55b0c-125">Následující návratový typ akce je synchronně výčtu během serializace:</span><span class="sxs-lookup"><span data-stu-id="55b0c-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="55b0c-126">Aby se zabránilo synchronnímu výčtu a blokování čekání na databázi v ASP.NET Core 2,2 a starších verzích `ToListAsync`, vyvolejte:</span><span class="sxs-lookup"><span data-stu-id="55b0c-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="55b0c-127">V ASP.NET Core 3,0 a novějším se `IAsyncEnumerable<T>` vrátí akce:</span><span class="sxs-lookup"><span data-stu-id="55b0c-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="55b0c-128">Již nevede k synchronní iteraci.</span><span class="sxs-lookup"><span data-stu-id="55b0c-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="55b0c-129">Se bude co nejefektivnější <xref:System.Collections.Generic.IEnumerable%601>jako vrácení.</span><span class="sxs-lookup"><span data-stu-id="55b0c-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="55b0c-130">ASP.NET Core 3,0 a novější vyrovnávací paměti výsledek následující akce před jejím poskytnutím serializátoru:</span><span class="sxs-lookup"><span data-stu-id="55b0c-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="55b0c-131">Zvažte deklaraci návratového typu podpisu akce, `IAsyncEnumerable<T>` aby se zajistila asynchronní iterace.</span><span class="sxs-lookup"><span data-stu-id="55b0c-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="55b0c-132">V konečném případě je režim iterace založen na vráceném základním konkrétním typu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="55b0c-133">MVC automaticky ukládá do vyrovnávací paměti jakýkoli konkrétní typ `IAsyncEnumerable<T>`, který implementuje.</span><span class="sxs-lookup"><span data-stu-id="55b0c-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="55b0c-134">Vezměte v úvahu následující akci, která vrací záznamy produktů s cenami za `IEnumerable<Product>`prodej jako:</span><span class="sxs-lookup"><span data-stu-id="55b0c-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="55b0c-135">`IAsyncEnumerable<Product>` Ekvivalent předchozí akce:</span><span class="sxs-lookup"><span data-stu-id="55b0c-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="55b0c-136">Obě předchozí akce nejsou blokující ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="55b0c-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="55b0c-137">Typ IActionResult</span><span class="sxs-lookup"><span data-stu-id="55b0c-137">IActionResult type</span></span>

<span data-ttu-id="55b0c-138">Návratový typ je vhodný, pokud je `ActionResult` možné v akci použít více návratových typů. <xref:Microsoft.AspNetCore.Mvc.IActionResult></span><span class="sxs-lookup"><span data-stu-id="55b0c-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="55b0c-139">`ActionResult` Typy reprezentují různé stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="55b0c-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="55b0c-140">Jakákoli Neabstraktní třída odvozená od `ActionResult` je platná jako platný návratový typ.</span><span class="sxs-lookup"><span data-stu-id="55b0c-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="55b0c-141">Některé běžné návratové typy v této kategorii <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> jsou (400) <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> , (404) a <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="55b0c-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="55b0c-142">Alternativně lze metody ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> třídě použít k vrácení `ActionResult` typů z akce.</span><span class="sxs-lookup"><span data-stu-id="55b0c-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="55b0c-143">Například `return BadRequest();` je zkrácený `return new BadRequestResult();`tvar.</span><span class="sxs-lookup"><span data-stu-id="55b0c-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="55b0c-144">Vzhledem k tomu, že v tomto typu akce existuje více návratových typů a cest, je nezbytné použít atribut [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="55b0c-144">Because there are multiple return types and paths in this type of action, liberal use of the [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="55b0c-145">Tento atribut vytváří podrobnější podrobnosti odpovědi na stránky pro nápovědu k webovému rozhraní API generované nástroji, jako je [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="55b0c-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="55b0c-146">`[ProducesResponseType]`Určuje známé typy a stavové kódy HTTP, které má akce vrátit.</span><span class="sxs-lookup"><span data-stu-id="55b0c-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="55b0c-147">Synchronní akce</span><span class="sxs-lookup"><span data-stu-id="55b0c-147">Synchronous action</span></span>

<span data-ttu-id="55b0c-148">Zvažte následující synchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="55b0c-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="55b0c-149">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="55b0c-149">In the preceding action:</span></span>

* <span data-ttu-id="55b0c-150">Pokud produkt reprezentovaný `id` neexistují v podkladovém úložišti dat, vrátí se stavový kód 404.</span><span class="sxs-lookup"><span data-stu-id="55b0c-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="55b0c-151">Metoda usnadnění je vyvolána jako zkrácený pro `return new NotFoundResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*></span><span class="sxs-lookup"><span data-stu-id="55b0c-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="55b0c-152">Stavový kód 200 se vrátí s `Product` objektem, když produkt existuje.</span><span class="sxs-lookup"><span data-stu-id="55b0c-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="55b0c-153">Metoda usnadnění je vyvolána jako zkrácený pro `return new OkObjectResult(product);`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*></span><span class="sxs-lookup"><span data-stu-id="55b0c-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="55b0c-154">Asynchronní akce</span><span class="sxs-lookup"><span data-stu-id="55b0c-154">Asynchronous action</span></span>

<span data-ttu-id="55b0c-155">Zvažte následující asynchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="55b0c-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="55b0c-156">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="55b0c-156">In the preceding action:</span></span>

* <span data-ttu-id="55b0c-157">Pokud popis produktu obsahuje "widget XYZ", vrátí se stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="55b0c-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="55b0c-158">Metoda usnadnění je vyvolána jako zkrácený pro `return new BadRequestResult();`. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*></span><span class="sxs-lookup"><span data-stu-id="55b0c-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="55b0c-159">Kód stavu 201 je vygenerován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou pohodlí při vytvoření produktu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="55b0c-160">Alternativa k volání `CreatedAtAction` je `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="55b0c-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="55b0c-161">V této cestě `Product` kódu je objekt uveden v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="55b0c-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="55b0c-162">Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu. `Location`</span><span class="sxs-lookup"><span data-stu-id="55b0c-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="55b0c-163">Například následující model označuje, že požadavky musí zahrnovat `Name` vlastnosti a. `Description`</span><span class="sxs-lookup"><span data-stu-id="55b0c-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="55b0c-164">Nepovedlo `Name` se `Description` poskytnout a v žádosti dojde k selhání ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="55b0c-165">Pokud je použit atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) v ASP.NET Core 2,1 nebo novějším, výsledkem chyb ověření modelu je 400 stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-165">If the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="55b0c-166">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="55b0c-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="55b0c-167">ActionResult\<T – typ ></span><span class="sxs-lookup"><span data-stu-id="55b0c-167">ActionResult\<T> type</span></span>

<span data-ttu-id="55b0c-168">ASP.NET Core 2,1 zavedla pro akce kontroleru webového rozhraní API > návratový typ [ActionResult\<T](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) .</span><span class="sxs-lookup"><span data-stu-id="55b0c-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="55b0c-169">Umožňuje vrátit typ odvozený z <xref:Microsoft.AspNetCore.Mvc.ActionResult> nebo vracet [konkrétní typ](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="55b0c-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="55b0c-170">`ActionResult<T>`nabízí následující výhody oproti [IActionResult typu](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="55b0c-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="55b0c-171">`Type` Vlastnost atributu [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) lze vyloučit.</span><span class="sxs-lookup"><span data-stu-id="55b0c-171">The [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="55b0c-172">Například `[ProducesResponseType(200, Type = typeof(Product))]` je zjednodušený pro `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="55b0c-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="55b0c-173">Očekávaný návratový typ akce je místo odvoditelné z `T` v. `ActionResult<T>`</span><span class="sxs-lookup"><span data-stu-id="55b0c-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="55b0c-174">[Operátory implicitního přetypování](/dotnet/csharp/language-reference/keywords/implicit) podporují převod obou `T` `ActionResult` i na `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="55b0c-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="55b0c-175">`T`převádí na <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, což znamená `return new ObjectResult(T);` , že je `return T;`zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="55b0c-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="55b0c-176">C#nepodporuje implicitní operátory přetypování na rozhraních.</span><span class="sxs-lookup"><span data-stu-id="55b0c-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="55b0c-177">V důsledku toho je nutné použít `ActionResult<T>`převod rozhraní na konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="55b0c-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="55b0c-178">Například použití `IEnumerable` v následujícím příkladu nefunguje:</span><span class="sxs-lookup"><span data-stu-id="55b0c-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="55b0c-179">Jednou z možností, jak opravit předchozí kód, je `_repository.GetProducts().ToList();`vrátit.</span><span class="sxs-lookup"><span data-stu-id="55b0c-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="55b0c-180">Většina akcí má určitý návratový typ.</span><span class="sxs-lookup"><span data-stu-id="55b0c-180">Most actions have a specific return type.</span></span> <span data-ttu-id="55b0c-181">Během provádění akce mohou nastat neočekávané podmínky. v takovém případě se konkrétní typ nevrátí.</span><span class="sxs-lookup"><span data-stu-id="55b0c-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="55b0c-182">Například vstupní parametr akce může selhat při ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="55b0c-183">V takovém případě je běžné vracet odpovídající `ActionResult` typ místo konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="55b0c-184">Synchronní akce</span><span class="sxs-lookup"><span data-stu-id="55b0c-184">Synchronous action</span></span>

<span data-ttu-id="55b0c-185">Zvažte synchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="55b0c-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="55b0c-186">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="55b0c-186">In the preceding action:</span></span>

* <span data-ttu-id="55b0c-187">Stavový kód 404 se vrátí, když produkt v databázi neexistuje.</span><span class="sxs-lookup"><span data-stu-id="55b0c-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="55b0c-188">Pokud produkt existuje, vrátí se stavový kód `Product` 200 s odpovídajícím objektem.</span><span class="sxs-lookup"><span data-stu-id="55b0c-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="55b0c-189">Před ASP.NET Core 2,1 `return product;` musí být `return Ok(product);`řádek.</span><span class="sxs-lookup"><span data-stu-id="55b0c-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="55b0c-190">Asynchronní akce</span><span class="sxs-lookup"><span data-stu-id="55b0c-190">Asynchronous action</span></span>

<span data-ttu-id="55b0c-191">Zvažte asynchronní akci, ve které existují dva možné návratové typy:</span><span class="sxs-lookup"><span data-stu-id="55b0c-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="55b0c-192">V předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="55b0c-192">In the preceding action:</span></span>

* <span data-ttu-id="55b0c-193">ASP.NET Core Runtime vrátí stavový<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>kód 400, když:</span><span class="sxs-lookup"><span data-stu-id="55b0c-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="55b0c-194">Atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) byl použit a ověření modelu se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="55b0c-194">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="55b0c-195">Popis produktu obsahuje "widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="55b0c-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="55b0c-196">Kód stavu 201 je generován <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> metodou při vytvoření produktu.</span><span class="sxs-lookup"><span data-stu-id="55b0c-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="55b0c-197">V této cestě `Product` kódu je objekt uveden v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="55b0c-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="55b0c-198">Poskytla se hlavička odpovědi obsahující nově vytvořenou adresu URL produktu. `Location`</span><span class="sxs-lookup"><span data-stu-id="55b0c-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="55b0c-199">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="55b0c-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
