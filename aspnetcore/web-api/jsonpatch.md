---
title: JsonPatch v ASP.NET Core Web API
author: rick-anderson
description: Naučte se zpracovávat požadavky na opravy JSON ve ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: cf1a00c1928652bf5210b2442087209e23b8868e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661782"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="6eff9-103">JsonPatch v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="6eff9-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="6eff9-104">[Dykstra](https://github.com/tdykstra) a [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6eff9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6eff9-105">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6eff9-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="6eff9-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="6eff9-106">Package installation</span></span>

<span data-ttu-id="6eff9-107">Podpora pro JsonPatch je povolená pomocí balíčku `Microsoft.AspNetCore.Mvc.NewtonsoftJson`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="6eff9-108">Chcete-li povolit tuto funkci, aplikace musí:</span><span class="sxs-lookup"><span data-stu-id="6eff9-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="6eff9-109">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) .</span><span class="sxs-lookup"><span data-stu-id="6eff9-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="6eff9-110">Aktualizujte metodu `Startup.ConfigureServices` projektu tak, aby zahrnovala volání `AddNewtonsoftJson`:</span><span class="sxs-lookup"><span data-stu-id="6eff9-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="6eff9-111">`AddNewtonsoftJson` je kompatibilní s metodami registrace služby MVC:</span><span class="sxs-lookup"><span data-stu-id="6eff9-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="6eff9-112">JsonPatch, AddNewtonsoftJson a System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="6eff9-113">`AddNewtonsoftJson` nahrazuje formátovací moduly vstupu a výstupu založené na `System.Text.Json` používané pro formátování **veškerého** obsahu JSON.</span><span class="sxs-lookup"><span data-stu-id="6eff9-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="6eff9-114">Chcete-li přidat podporu pro `JsonPatch` pomocí `Newtonsoft.Json`, zatímco ostatní formátovací moduly zůstaly beze změny, aktualizujte `Startup.ConfigureServices` projektu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="6eff9-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="6eff9-115">Předchozí kód vyžaduje odkaz na [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) a následující příkazy using:</span><span class="sxs-lookup"><span data-stu-id="6eff9-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="6eff9-116">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="6eff9-116">PATCH HTTP request method</span></span>

<span data-ttu-id="6eff9-117">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="6eff9-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6eff9-118">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="6eff9-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6eff9-119">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-119">JSON Patch</span></span>

<span data-ttu-id="6eff9-120">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="6eff9-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6eff9-121">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="6eff9-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6eff9-122">Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="6eff9-123">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="6eff9-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6eff9-124">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="6eff9-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6eff9-125">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6eff9-126">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-126">In the preceding JSON:</span></span>

* <span data-ttu-id="6eff9-127">Vlastnost `op` označuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="6eff9-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6eff9-128">Vlastnost `path` určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="6eff9-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6eff9-129">Vlastnost `value` poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6eff9-130">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="6eff9-130">Resource after patch</span></span>

<span data-ttu-id="6eff9-131">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="6eff9-132">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6eff9-133">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="6eff9-133">Path syntax</span></span>

<span data-ttu-id="6eff9-134">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6eff9-135">například `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6eff9-136">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6eff9-137">První prvek `addresses` pole by byl `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6eff9-138">Chcete-li `add` na konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6eff9-139">Operace</span><span class="sxs-lookup"><span data-stu-id="6eff9-139">Operations</span></span>

<span data-ttu-id="6eff9-140">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="6eff9-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6eff9-141">Operace</span><span class="sxs-lookup"><span data-stu-id="6eff9-141">Operation</span></span>  | <span data-ttu-id="6eff9-142">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="6eff9-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="6eff9-143">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-143">Add a property or array element.</span></span> <span data-ttu-id="6eff9-144">Pro existující vlastnost: nastavte hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="6eff9-145">Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="6eff9-146">Stejné jako `remove` následované `add` na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="6eff9-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="6eff9-147">Stejné jako `remove` ze zdroje, za nímž následuje `add` k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="6eff9-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="6eff9-148">Stejné jako `add` k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="6eff9-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="6eff9-149">Vrátí stavový kód úspěšného zpracování, pokud je hodnota na `path` = poskytnutá `value`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="6eff9-150">JsonPatch v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eff9-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="6eff9-151">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="6eff9-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6eff9-152">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="6eff9-152">Action method code</span></span>

<span data-ttu-id="6eff9-153">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6eff9-154">Je opatřen s použitím atributu `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6eff9-155">Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6eff9-156">Zavolá `ApplyTo` v dokumentu opravy, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="6eff9-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6eff9-157">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="6eff9-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6eff9-158">Tento kód z ukázkové aplikace funguje s následujícím modelem `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6eff9-159">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="6eff9-159">The sample action method:</span></span>

* <span data-ttu-id="6eff9-160">Vytvoří `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6eff9-161">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-161">Applies the patch.</span></span>
* <span data-ttu-id="6eff9-162">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="6eff9-163">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6eff9-164">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="6eff9-164">Model state</span></span>

<span data-ttu-id="6eff9-165">Předchozí příklad metody volá přetížení `ApplyTo`, které jako jeden z jeho parametrů přijímá stav modelu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6eff9-166">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="6eff9-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6eff9-167">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro operaci `test`:</span><span class="sxs-lookup"><span data-stu-id="6eff9-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6eff9-168">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="6eff9-168">Dynamic objects</span></span>

<span data-ttu-id="6eff9-169">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="6eff9-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6eff9-170">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="6eff9-170">The add operation</span></span>

* <span data-ttu-id="6eff9-171">Pokud `path` odkazuje na prvek pole: vloží nový prvek před první zadaný pomocí `path`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6eff9-172">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6eff9-173">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="6eff9-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6eff9-174">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6eff9-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6eff9-175">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6eff9-176">Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá objekt `Order` na konec pole `Orders`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6eff9-177">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="6eff9-177">The remove operation</span></span>

* <span data-ttu-id="6eff9-178">Pokud `path` odkazuje na prvek pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="6eff9-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6eff9-179">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6eff9-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="6eff9-180">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6eff9-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6eff9-181">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="6eff9-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6eff9-182">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="6eff9-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6eff9-183">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6eff9-184">Následující ukázková sada dokumentů má `CustomerName` na hodnotu null a odstraní `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6eff9-185">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="6eff9-185">The replace operation</span></span>

<span data-ttu-id="6eff9-186">Tato operace je funkčně stejná jako `remove` následované `add`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6eff9-187">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]`novým objektem `Order`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6eff9-188">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="6eff9-188">The move operation</span></span>

* <span data-ttu-id="6eff9-189">Pokud `path` odkazuje na prvek pole: zkopíruje `from` elementu do umístění `path` elementu a pak spustí operaci `remove` na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6eff9-190">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu vlastnosti `from` na vlastnost `path` a potom spustí operaci `remove` na vlastnosti `from`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6eff9-191">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6eff9-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6eff9-192">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6eff9-193">Pokud je prostředek k opravě dynamický objekt: zkopíruje `from` vlastnost do umístění určeného parametrem `path`a poté spustí operaci `remove` pro vlastnost `from`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6eff9-194">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="6eff9-194">The following sample patch document:</span></span>

* <span data-ttu-id="6eff9-195">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6eff9-196">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="6eff9-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6eff9-197">Přesune `Orders[1]` do `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6eff9-198">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="6eff9-198">The copy operation</span></span>

<span data-ttu-id="6eff9-199">Tato operace je funkčně stejná jako operace `move`, aniž by to mělo poslední `remove` krok.</span><span class="sxs-lookup"><span data-stu-id="6eff9-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6eff9-200">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="6eff9-200">The following sample patch document:</span></span>

* <span data-ttu-id="6eff9-201">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6eff9-202">Vloží kopii `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6eff9-203">Operace testu</span><span class="sxs-lookup"><span data-stu-id="6eff9-203">The test operation</span></span>

<span data-ttu-id="6eff9-204">Pokud hodnota v umístění označeném `path` se liší od hodnoty zadané v `value`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6eff9-205">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="6eff9-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6eff9-206">Operace `test` se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6eff9-207">Následující ukázkový dokument opravy nemá žádný vliv, pokud je počáteční hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="6eff9-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6eff9-208">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="6eff9-208">Get the code</span></span>

<span data-ttu-id="6eff9-209">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="6eff9-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="6eff9-210">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6eff9-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6eff9-211">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="6eff9-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6eff9-212">Adresa URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6eff9-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6eff9-213">Metoda HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6eff9-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6eff9-214">Záhlaví: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6eff9-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6eff9-215">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6eff9-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eff9-216">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6eff9-216">Additional resources</span></span>

* [<span data-ttu-id="6eff9-217">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="6eff9-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6eff9-218">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="6eff9-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6eff9-219">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="6eff9-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6eff9-220">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6eff9-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6eff9-221">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="6eff9-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6eff9-222">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6eff9-223">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6eff9-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="6eff9-224">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="6eff9-224">PATCH HTTP request method</span></span>

<span data-ttu-id="6eff9-225">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="6eff9-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="6eff9-226">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="6eff9-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="6eff9-227">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-227">JSON Patch</span></span>

<span data-ttu-id="6eff9-228">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="6eff9-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="6eff9-229">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="6eff9-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="6eff9-230">Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="6eff9-231">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="6eff9-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="6eff9-232">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="6eff9-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="6eff9-233">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="6eff9-234">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-234">In the preceding JSON:</span></span>

* <span data-ttu-id="6eff9-235">Vlastnost `op` označuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="6eff9-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="6eff9-236">Vlastnost `path` určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="6eff9-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="6eff9-237">Vlastnost `value` poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="6eff9-238">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="6eff9-238">Resource after patch</span></span>

<span data-ttu-id="6eff9-239">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="6eff9-240">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="6eff9-241">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="6eff9-241">Path syntax</span></span>

<span data-ttu-id="6eff9-242">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="6eff9-243">například `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="6eff9-244">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="6eff9-245">První prvek `addresses` pole by byl `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="6eff9-246">Chcete-li `add` na konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="6eff9-247">Operace</span><span class="sxs-lookup"><span data-stu-id="6eff9-247">Operations</span></span>

<span data-ttu-id="6eff9-248">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="6eff9-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="6eff9-249">Operace</span><span class="sxs-lookup"><span data-stu-id="6eff9-249">Operation</span></span>  | <span data-ttu-id="6eff9-250">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="6eff9-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="6eff9-251">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-251">Add a property or array element.</span></span> <span data-ttu-id="6eff9-252">Pro existující vlastnost: nastavte hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="6eff9-253">Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="6eff9-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="6eff9-254">Stejné jako `remove` následované `add` na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="6eff9-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="6eff9-255">Stejné jako `remove` ze zdroje, za nímž následuje `add` k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="6eff9-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="6eff9-256">Stejné jako `add` k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="6eff9-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="6eff9-257">Vrátí stavový kód úspěšného zpracování, pokud je hodnota na `path` = poskytnutá `value`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="6eff9-258">JsonPatch v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6eff9-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="6eff9-259">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="6eff9-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="6eff9-260">Balíček obsahuje [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) Metapackage.</span><span class="sxs-lookup"><span data-stu-id="6eff9-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="6eff9-261">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="6eff9-261">Action method code</span></span>

<span data-ttu-id="6eff9-262">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="6eff9-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="6eff9-263">Je opatřen s použitím atributu `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="6eff9-264">Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="6eff9-265">Zavolá `ApplyTo` v dokumentu opravy, aby se změny projevily.</span><span class="sxs-lookup"><span data-stu-id="6eff9-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="6eff9-266">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="6eff9-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="6eff9-267">Tento kód z ukázkové aplikace funguje s následujícím modelem `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="6eff9-268">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="6eff9-268">The sample action method:</span></span>

* <span data-ttu-id="6eff9-269">Vytvoří `Customer`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="6eff9-270">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-270">Applies the patch.</span></span>
* <span data-ttu-id="6eff9-271">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="6eff9-272">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="6eff9-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="6eff9-273">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="6eff9-273">Model state</span></span>

<span data-ttu-id="6eff9-274">Předchozí příklad metody volá přetížení `ApplyTo`, které jako jeden z jeho parametrů přijímá stav modelu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="6eff9-275">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="6eff9-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="6eff9-276">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro operaci `test`:</span><span class="sxs-lookup"><span data-stu-id="6eff9-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="6eff9-277">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="6eff9-277">Dynamic objects</span></span>

<span data-ttu-id="6eff9-278">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="6eff9-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="6eff9-279">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="6eff9-279">The add operation</span></span>

* <span data-ttu-id="6eff9-280">Pokud `path` odkazuje na prvek pole: vloží nový prvek před první zadaný pomocí `path`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="6eff9-281">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="6eff9-282">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="6eff9-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="6eff9-283">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6eff9-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="6eff9-284">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="6eff9-285">Následující ukázkový dokument opravy nastaví hodnotu `CustomerName` a přidá objekt `Order` na konec pole `Orders`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="6eff9-286">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="6eff9-286">The remove operation</span></span>

* <span data-ttu-id="6eff9-287">Pokud `path` odkazuje na prvek pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="6eff9-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="6eff9-288">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6eff9-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="6eff9-289">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6eff9-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="6eff9-290">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="6eff9-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="6eff9-291">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="6eff9-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="6eff9-292">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="6eff9-293">Následující ukázková sada dokumentů má `CustomerName` na hodnotu null a odstraní `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="6eff9-294">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="6eff9-294">The replace operation</span></span>

<span data-ttu-id="6eff9-295">Tato operace je funkčně stejná jako `remove` následované `add`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="6eff9-296">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]`novým objektem `Order`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="6eff9-297">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="6eff9-297">The move operation</span></span>

* <span data-ttu-id="6eff9-298">Pokud `path` odkazuje na prvek pole: zkopíruje `from` elementu do umístění `path` elementu a pak spustí operaci `remove` na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="6eff9-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="6eff9-299">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu vlastnosti `from` na vlastnost `path` a potom spustí operaci `remove` na vlastnosti `from`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="6eff9-300">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6eff9-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="6eff9-301">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="6eff9-302">Pokud je prostředek k opravě dynamický objekt: zkopíruje `from` vlastnost do umístění určeného parametrem `path`a poté spustí operaci `remove` pro vlastnost `from`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="6eff9-303">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="6eff9-303">The following sample patch document:</span></span>

* <span data-ttu-id="6eff9-304">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6eff9-305">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="6eff9-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="6eff9-306">Přesune `Orders[1]` do `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="6eff9-307">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="6eff9-307">The copy operation</span></span>

<span data-ttu-id="6eff9-308">Tato operace je funkčně stejná jako operace `move`, aniž by to mělo poslední `remove` krok.</span><span class="sxs-lookup"><span data-stu-id="6eff9-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="6eff9-309">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="6eff9-309">The following sample patch document:</span></span>

* <span data-ttu-id="6eff9-310">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="6eff9-311">Vloží kopii `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="6eff9-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="6eff9-312">Operace testu</span><span class="sxs-lookup"><span data-stu-id="6eff9-312">The test operation</span></span>

<span data-ttu-id="6eff9-313">Pokud hodnota v umístění označeném `path` se liší od hodnoty zadané v `value`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6eff9-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="6eff9-314">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="6eff9-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="6eff9-315">Operace `test` se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="6eff9-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="6eff9-316">Následující ukázkový dokument opravy nemá žádný vliv, pokud je počáteční hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="6eff9-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="6eff9-317">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="6eff9-317">Get the code</span></span>

<span data-ttu-id="6eff9-318">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="6eff9-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="6eff9-319">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6eff9-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6eff9-320">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="6eff9-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="6eff9-321">Adresa URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="6eff9-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="6eff9-322">Metoda HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="6eff9-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="6eff9-323">Záhlaví: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="6eff9-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="6eff9-324">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6eff9-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6eff9-325">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6eff9-325">Additional resources</span></span>

* [<span data-ttu-id="6eff9-326">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="6eff9-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="6eff9-327">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="6eff9-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="6eff9-328">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="6eff9-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="6eff9-329">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="6eff9-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="6eff9-330">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="6eff9-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="6eff9-331">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="6eff9-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
