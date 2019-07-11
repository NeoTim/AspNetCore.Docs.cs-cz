---
title: JsonPatch in ASP.NET Core web API
author: tdykstra
description: Zjistěte, jak zpracovávat požadavky oprava JSON v webovému rozhraní API ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815543"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="f3172-103">JsonPatch in ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="f3172-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="f3172-104">podle [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f3172-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f3172-105">Tento článek vysvětluje, jak zpracovávat požadavky oprava JSON v webovému rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3172-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="f3172-106">Metoda požadavku PATCH HTTP</span><span class="sxs-lookup"><span data-stu-id="f3172-106">PATCH HTTP request method</span></span>

<span data-ttu-id="f3172-107">PUT a [oprava](https://tools.ietf.org/html/rfc5789) metody se používají k aktualizaci existující prostředek.</span><span class="sxs-lookup"><span data-stu-id="f3172-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f3172-108">Rozdíl mezi nimi je, že PUT nahradí celý zdroj, zatímco oprava určuje pouze změny.</span><span class="sxs-lookup"><span data-stu-id="f3172-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f3172-109">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="f3172-109">JSON Patch</span></span>

<span data-ttu-id="f3172-110">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro zadávání aktualizace použije k prostředku.</span><span class="sxs-lookup"><span data-stu-id="f3172-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f3172-111">Dokument opravy JSON obsahuje celou řadu *operace*.</span><span class="sxs-lookup"><span data-stu-id="f3172-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f3172-112">Každou operaci identifikuje konkrétní typ změny, jako například přidat k elementu pole nebo nahraďte hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f3172-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="f3172-113">Například následující dokumenty JSON představují prostředek, dokument opravy JSON pro daný zdroj a výsledek použití operace opravy.</span><span class="sxs-lookup"><span data-stu-id="f3172-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f3172-114">Příklad prostředků</span><span class="sxs-lookup"><span data-stu-id="f3172-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f3172-115">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="f3172-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f3172-116">Ve výše uvedeného kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="f3172-116">In the preceding JSON:</span></span>

* <span data-ttu-id="f3172-117">`op` Vlastnost určuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="f3172-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f3172-118">`path` Vlastnost označuje elementu, který chcete aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="f3172-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f3172-119">`value` Vlastnost obsahuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f3172-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f3172-120">Po opravě prostředku</span><span class="sxs-lookup"><span data-stu-id="f3172-120">Resource after patch</span></span>

<span data-ttu-id="f3172-121">Zde je prostředek po použití předchozí dokument opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="f3172-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="f3172-122">Změny provedené při použití dokument opravy JSON pro prostředek jsou atomické: Pokud všechny operace v seznamu selže, použije se žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="f3172-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f3172-123">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="f3172-123">Path syntax</span></span>

<span data-ttu-id="f3172-124">[Cesta](https://tools.ietf.org/html/rfc6901) vlastnost objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="f3172-124">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f3172-125">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="f3172-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f3172-126">Založený na nule indexy se používají k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="f3172-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f3172-127">První prvek `addresses` pole by na `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="f3172-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f3172-128">K `add` na konec pole používejte pomlčkou (-) místo číslo indexu: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="f3172-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f3172-129">Operace</span><span class="sxs-lookup"><span data-stu-id="f3172-129">Operations</span></span>

<span data-ttu-id="f3172-130">Následující tabulka ukazuje podporované operace definované v [oprava JSON specifikace](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="f3172-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f3172-131">Operace</span><span class="sxs-lookup"><span data-stu-id="f3172-131">Operation</span></span>  | <span data-ttu-id="f3172-132">Poznámky</span><span class="sxs-lookup"><span data-stu-id="f3172-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="f3172-133">Přidáte vlastnost nebo pole elementu.</span><span class="sxs-lookup"><span data-stu-id="f3172-133">Add a property or array element.</span></span> <span data-ttu-id="f3172-134">Pro existující vlastnost: nastavení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f3172-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="f3172-135">Odeberte element vlastnost nebo pole.</span><span class="sxs-lookup"><span data-stu-id="f3172-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="f3172-136">Stejné jako `remove` následovaný `add` ve stejném umístění.</span><span class="sxs-lookup"><span data-stu-id="f3172-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="f3172-137">Stejné jako `remove` ze zdroje, za nímž následuje `add` do cíle pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f3172-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="f3172-138">Stejné jako `add` do cíle pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f3172-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="f3172-139">Vrátí stavový kód úspěchu, pokud hodnotu `path` = zadaný `value`.</span><span class="sxs-lookup"><span data-stu-id="f3172-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="f3172-140">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3172-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="f3172-141">ASP.NET Core provádění opravu JSON je součástí [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f3172-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="f3172-142">Je součástí balíčku [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) Microsoft.aspnetcore.all.</span><span class="sxs-lookup"><span data-stu-id="f3172-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f3172-143">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="f3172-143">Action method code</span></span>

<span data-ttu-id="f3172-144">V rozhraní API adaptéru metodu akce pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="f3172-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f3172-145">Je opatřen poznámkou `HttpPatch` atribut.</span><span class="sxs-lookup"><span data-stu-id="f3172-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f3172-146">Přijímá `JsonPatchDocument<T>`, obvykle s [FromBody].</span><span class="sxs-lookup"><span data-stu-id="f3172-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="f3172-147">Volání `ApplyTo` na dokument opravy a změny aplikujte.</span><span class="sxs-lookup"><span data-stu-id="f3172-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f3172-148">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f3172-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f3172-149">Tento kód z ukázkové aplikace funguje u následujících `Customer` modelu.</span><span class="sxs-lookup"><span data-stu-id="f3172-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f3172-150">Ukázka metody akce:</span><span class="sxs-lookup"><span data-stu-id="f3172-150">The sample action method:</span></span>

* <span data-ttu-id="f3172-151">Vytvoří `Customer`.</span><span class="sxs-lookup"><span data-stu-id="f3172-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f3172-152">Oprava se vztahuje.</span><span class="sxs-lookup"><span data-stu-id="f3172-152">Applies the patch.</span></span>
* <span data-ttu-id="f3172-153">Vrátí výsledek v textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f3172-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="f3172-154">V reálné aplikaci byste kód načtou data z úložiště, jako je databáze a aktualizovat databázi po použití opravy.</span><span class="sxs-lookup"><span data-stu-id="f3172-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f3172-155">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="f3172-155">Model state</span></span>

<span data-ttu-id="f3172-156">V předchozím příkladu metoda akce volá přetížení `ApplyTo` stavu modelu, která má jako jeden ze svých parametrů.</span><span class="sxs-lookup"><span data-stu-id="f3172-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f3172-157">Pomocí této možnosti můžete získat chybové zprávy v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f3172-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f3172-158">Následující příklad ukazuje text 400 Chybný požadavek odpovědi pro `test` operace:</span><span class="sxs-lookup"><span data-stu-id="f3172-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f3172-159">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="f3172-159">Dynamic objects</span></span>

<span data-ttu-id="f3172-160">V následujícím příkladu metoda akce ukazuje, jak použít opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="f3172-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f3172-161">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="f3172-161">The add operation</span></span>

* <span data-ttu-id="f3172-162">Pokud `path` odkazuje na element pole: Vloží nový prvek před vlákna zadaného parametrem `path`.</span><span class="sxs-lookup"><span data-stu-id="f3172-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f3172-163">Pokud `path` odkazuje na vlastnost: Nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f3172-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f3172-164">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="f3172-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f3172-165">Pokud dynamických objektů je prostředkem o opravu: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f3172-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f3172-166">Pokud statických objektů je prostředkem o opravu: požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="f3172-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f3172-167">Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá `Order` objekt na konec objektu `Orders` pole.</span><span class="sxs-lookup"><span data-stu-id="f3172-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f3172-168">Operace odebrání</span><span class="sxs-lookup"><span data-stu-id="f3172-168">The remove operation</span></span>

* <span data-ttu-id="f3172-169">Pokud `path` odkazuje na element pole: Odebere element.</span><span class="sxs-lookup"><span data-stu-id="f3172-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f3172-170">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f3172-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="f3172-171">Pokud dynamických objektů je prostředkem o opravu: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f3172-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f3172-172">Pokud je prostředkem o opravu statický objekt:</span><span class="sxs-lookup"><span data-stu-id="f3172-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="f3172-173">Pokud je vlastnost s možnou hodnotou NULL: Nastaví na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f3172-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f3172-174">Když se tato vlastnost je null, nastaví `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="f3172-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f3172-175">Následující ukázkové sady dokumentů oprava `CustomerName` null a odstraní `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="f3172-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f3172-176">Operaci nahrazení</span><span class="sxs-lookup"><span data-stu-id="f3172-176">The replace operation</span></span>

<span data-ttu-id="f3172-177">Tato operace je funkčně stejný jako `remove` následovaný `add`.</span><span class="sxs-lookup"><span data-stu-id="f3172-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f3172-178">Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a nahradí `Orders[0]`s novou `Order` objektu.</span><span class="sxs-lookup"><span data-stu-id="f3172-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f3172-179">Operace přesunu</span><span class="sxs-lookup"><span data-stu-id="f3172-179">The move operation</span></span>

* <span data-ttu-id="f3172-180">Pokud `path` odkazuje na element pole: kopie `from` element umístění `path` elementu, pak spustí `remove` operace `from` element.</span><span class="sxs-lookup"><span data-stu-id="f3172-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f3172-181">Pokud `path` odkazuje na vlastnost: kopíruje hodnotu `from` vlastnost `path` vlastnost, pak spustí `remove` operace `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f3172-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f3172-182">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f3172-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f3172-183">Pokud statických objektů je prostředkem o opravu: požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="f3172-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f3172-184">Pokud dynamických objektů je prostředkem o opravu: kopie `from` vlastnost umístění indikován `path`, pak spustí `remove` operace `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f3172-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f3172-185">Dokument opravy následující ukázka:</span><span class="sxs-lookup"><span data-stu-id="f3172-185">The following sample patch document:</span></span>

* <span data-ttu-id="f3172-186">Zkopíruje hodnoty `Orders[0].OrderName` k `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="f3172-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f3172-187">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f3172-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f3172-188">Přesune `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="f3172-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f3172-189">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="f3172-189">The copy operation</span></span>

<span data-ttu-id="f3172-190">Tato operace je funkčně stejný jako `move` operaci bez finální `remove` kroku.</span><span class="sxs-lookup"><span data-stu-id="f3172-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f3172-191">Dokument opravy následující ukázka:</span><span class="sxs-lookup"><span data-stu-id="f3172-191">The following sample patch document:</span></span>

* <span data-ttu-id="f3172-192">Zkopíruje hodnoty `Orders[0].OrderName` k `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="f3172-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f3172-193">Vloží kopii `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="f3172-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f3172-194">Testovací operace</span><span class="sxs-lookup"><span data-stu-id="f3172-194">The test operation</span></span>

<span data-ttu-id="f3172-195">Pokud je hodnota v umístění indikován `path` se liší od hodnoty podle `value`, požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="f3172-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f3172-196">V takovém případě celý požadavek PATCH selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.</span><span class="sxs-lookup"><span data-stu-id="f3172-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f3172-197">`test` Operace se běžně používá k zabránění aktualizace, když dojde ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f3172-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f3172-198">Následující ukázkový dokument opravy nemá žádný vliv, pokud počáteční hodnota `CustomerName` je "John", protože se test nezdaří:</span><span class="sxs-lookup"><span data-stu-id="f3172-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f3172-199">Získat kód</span><span class="sxs-lookup"><span data-stu-id="f3172-199">Get the code</span></span>

<span data-ttu-id="f3172-200">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="f3172-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="f3172-201">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f3172-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f3172-202">Testování ukázky, spusťte aplikaci a odesílat požadavky HTTP s následujícími nastaveními:</span><span class="sxs-lookup"><span data-stu-id="f3172-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f3172-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f3172-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f3172-204">Metoda HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="f3172-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f3172-205">Záhlaví: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f3172-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f3172-206">Text: Zkopírujte a vložte jednou z ukázek dokumentu opravy JSON z *JSON* složky projektu.</span><span class="sxs-lookup"><span data-stu-id="f3172-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3172-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f3172-207">Additional resources</span></span>

* [<span data-ttu-id="f3172-208">Oprava IETF RFC 5789 specifikace – metoda</span><span class="sxs-lookup"><span data-stu-id="f3172-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f3172-209">Specifikaci IETF RFC 6902 JSON opravy</span><span class="sxs-lookup"><span data-stu-id="f3172-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f3172-210">Oprava JSON 6901 IETF RFC specifikace formátu cesty</span><span class="sxs-lookup"><span data-stu-id="f3172-210">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f3172-211">[Dokumentace ke službě oprava JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="f3172-211">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="f3172-212">Obsahuje odkazy na prostředky pro vytváření dokumentů oprava JSON.</span><span class="sxs-lookup"><span data-stu-id="f3172-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f3172-213">ASP.NET Core oprava JSON zdrojového kódu</span><span class="sxs-lookup"><span data-stu-id="f3172-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
