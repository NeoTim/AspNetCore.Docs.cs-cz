---
title: JsonPatch v ASP.NET základní webové rozhraní API
author: rick-anderson
description: Zjistěte, jak zpracovat požadavky na opravu JSON v ASP.NET základníwebové rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625871"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="7c85e-103">JsonPatch v ASP.NET základní webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="7c85e-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="7c85e-104">Tom [Dykstra](https://github.com/tdykstra) a [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="7c85e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c85e-105">Tento článek vysvětluje, jak zpracovat požadavky na opravu JSON v ASP.NET základní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c85e-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="7c85e-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="7c85e-106">Package installation</span></span>

<span data-ttu-id="7c85e-107">Chcete-li ve své aplikaci povolit podporu aplikace JSON Patch, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="7c85e-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="7c85e-108">Nainstalujte balíček [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="7c85e-108">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="7c85e-109">Aktualizujte `Startup.ConfigureServices` metodu projektu <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>tak, aby volala .</span><span class="sxs-lookup"><span data-stu-id="7c85e-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="7c85e-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7c85e-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="7c85e-111">`AddNewtonsoftJson`je kompatibilní s metodami registrace služby MVC:</span><span class="sxs-lookup"><span data-stu-id="7c85e-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="7c85e-112">JSON Patch, AddNewtonsoftJson a System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="7c85e-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="7c85e-113">`AddNewtonsoftJson`nahradí `System.Text.Json`vstupní a výstupní promorávací věci používané pro formátování **veškerého** obsahu JSON.</span><span class="sxs-lookup"><span data-stu-id="7c85e-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="7c85e-114">Chcete-li přidat podporu pro `Newtonsoft.Json`JSON Patch pomocí , zatímco ostatní `Startup.ConfigureServices` formatters beze změny, aktualizujte metodu projektu takto:</span><span class="sxs-lookup"><span data-stu-id="7c85e-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="7c85e-115">Předchozí kód vyžaduje `Microsoft.AspNetCore.Mvc.NewtonsoftJson` balíček a `using` následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="7c85e-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="7c85e-116">Metoda požadavku PATCH HTTP</span><span class="sxs-lookup"><span data-stu-id="7c85e-116">PATCH HTTP request method</span></span>

<span data-ttu-id="7c85e-117">Metody PUT a [PATCH](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="7c85e-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="7c85e-118">Rozdíl mezi nimi je, že PUT nahradí celý prostředek, zatímco PATCH určuje pouze změny.</span><span class="sxs-lookup"><span data-stu-id="7c85e-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="7c85e-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="7c85e-119">JSON Patch</span></span>

<span data-ttu-id="7c85e-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které mají být použity pro prostředek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="7c85e-121">Dokument Záplaty JSON má řadu *operací*.</span><span class="sxs-lookup"><span data-stu-id="7c85e-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="7c85e-122">Každá operace identifikuje určitý typ změny.</span><span class="sxs-lookup"><span data-stu-id="7c85e-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="7c85e-123">Příklady takových změn zahrnují přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="7c85e-124">Například následující dokumenty JSON představují prostředek, dokument Záplata JSON pro prostředek a výsledek použití operací Opravy.</span><span class="sxs-lookup"><span data-stu-id="7c85e-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="7c85e-125">Příklad zdroje</span><span class="sxs-lookup"><span data-stu-id="7c85e-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="7c85e-126">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="7c85e-127">V předchozím JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-127">In the preceding JSON:</span></span>

* <span data-ttu-id="7c85e-128">Vlastnost `op` označuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="7c85e-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="7c85e-129">Vlastnost `path` označuje prvek aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="7c85e-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="7c85e-130">Vlastnost `value` poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="7c85e-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="7c85e-131">Zdroj po opravě</span><span class="sxs-lookup"><span data-stu-id="7c85e-131">Resource after patch</span></span>

<span data-ttu-id="7c85e-132">Zde je zdroj po použití předchozího dokumentu Opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="7c85e-133">Změny provedené použitím dokumentu Záplaty JSON na prostředek jsou atomické.</span><span class="sxs-lookup"><span data-stu-id="7c85e-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="7c85e-134">Pokud se některá operace v seznamu nezdaří, nebude použita žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="7c85e-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="7c85e-135">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="7c85e-135">Path syntax</span></span>

<span data-ttu-id="7c85e-136">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="7c85e-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="7c85e-137">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="7c85e-138">Indexy založené na nule se používají k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="7c85e-139">První prvek `addresses` pole by se `/addresses/0`na .</span><span class="sxs-lookup"><span data-stu-id="7c85e-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="7c85e-140">Na `add` konec pole použijte místo čísla`-` `/addresses/-`indexu pomlčku ( ).</span><span class="sxs-lookup"><span data-stu-id="7c85e-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="7c85e-141">Operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-141">Operations</span></span>

<span data-ttu-id="7c85e-142">V následující tabulce jsou uvedeny podporované operace definované ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="7c85e-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="7c85e-143">Operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-143">Operation</span></span>  | <span data-ttu-id="7c85e-144">Poznámky</span><span class="sxs-lookup"><span data-stu-id="7c85e-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="7c85e-145">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-145">Add a property or array element.</span></span> <span data-ttu-id="7c85e-146">Pro existující vlastnost: set value.</span><span class="sxs-lookup"><span data-stu-id="7c85e-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="7c85e-147">Odeberte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="7c85e-148">Stejné `remove` jako `add` na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="7c85e-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="7c85e-149">Stejné `remove` jako ze `add` zdroje následuje cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="7c85e-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="7c85e-150">Stejné `add` jako k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="7c85e-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="7c85e-151">Vrátit kód stavu úspěchu, pokud hodnota na `path` = za předpokladu `value`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="7c85e-152">JSON Patch v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="7c85e-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="7c85e-153">ASP.NET Core implementace JSON Patch je k dispozici v balíčku [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="7c85e-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="7c85e-154">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="7c85e-154">Action method code</span></span>

<span data-ttu-id="7c85e-155">V řadiči rozhraní API metoda akce pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="7c85e-156">Je anotován `HttpPatch` s atributem.</span><span class="sxs-lookup"><span data-stu-id="7c85e-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="7c85e-157">Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="7c85e-158">Zavolá `ApplyTo` dokument opravy použít změny.</span><span class="sxs-lookup"><span data-stu-id="7c85e-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="7c85e-159">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="7c85e-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="7c85e-160">Tento kód z ukázkové aplikace `Customer` funguje s následujícím modelem:</span><span class="sxs-lookup"><span data-stu-id="7c85e-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="7c85e-161">Ukázková metoda akce:</span><span class="sxs-lookup"><span data-stu-id="7c85e-161">The sample action method:</span></span>

* <span data-ttu-id="7c85e-162">Vytvoří `Customer`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="7c85e-163">Aplikuje náplast.</span><span class="sxs-lookup"><span data-stu-id="7c85e-163">Applies the patch.</span></span>
* <span data-ttu-id="7c85e-164">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c85e-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="7c85e-165">V reálné aplikaci by kód načetl data z úložiště, jako je například databáze, a po použití opravy databázi aktualizoval.</span><span class="sxs-lookup"><span data-stu-id="7c85e-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="7c85e-166">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="7c85e-166">Model state</span></span>

<span data-ttu-id="7c85e-167">Příklad předchozí metody akce volá `ApplyTo` přetížení, které trvá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="7c85e-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="7c85e-168">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="7c85e-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="7c85e-169">Následující příklad ukazuje tělo 400 chybný požadavek `test` odpověď pro operaci:</span><span class="sxs-lookup"><span data-stu-id="7c85e-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="7c85e-170">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="7c85e-170">Dynamic objects</span></span>

<span data-ttu-id="7c85e-171">Následující příklad metody akce ukazuje, jak aplikovat opravu na dynamický objekt:</span><span class="sxs-lookup"><span data-stu-id="7c85e-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="7c85e-172">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="7c85e-172">The add operation</span></span>

* <span data-ttu-id="7c85e-173">Pokud `path` odkazuje na prvek pole: vloží nový prvek `path`před ten, který je určen .</span><span class="sxs-lookup"><span data-stu-id="7c85e-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="7c85e-174">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="7c85e-175">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="7c85e-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="7c85e-176">Pokud prostředek k opravě je dynamický objekt: přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="7c85e-177">Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="7c85e-178">Následující ukázkový dokument opravy `CustomerName` nastaví `Order` hodnotu a přidá `Orders` objekt na konec pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="7c85e-179">Operace odebrání</span><span class="sxs-lookup"><span data-stu-id="7c85e-179">The remove operation</span></span>

* <span data-ttu-id="7c85e-180">Pokud `path` odkazuje na prvek pole: odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="7c85e-181">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7c85e-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="7c85e-182">Pokud prostředek k opravě je dynamický objekt: odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="7c85e-183">Pokud je prostředek k opravě statický objekt:</span><span class="sxs-lookup"><span data-stu-id="7c85e-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="7c85e-184">Pokud je vlastnost nullable: nastaví ji na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="7c85e-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="7c85e-185">Pokud je vlastnost nenulovatelná, `default<T>`nastaví ji na .</span><span class="sxs-lookup"><span data-stu-id="7c85e-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="7c85e-186">Následující ukázkový dokument `CustomerName` opravy se `Orders[0]`nastaví na hodnotu null a odstraní :</span><span class="sxs-lookup"><span data-stu-id="7c85e-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="7c85e-187">Operace výměny</span><span class="sxs-lookup"><span data-stu-id="7c85e-187">The replace operation</span></span>

<span data-ttu-id="7c85e-188">Tato operace je funkčně `remove` stejná `add`jako následuje .</span><span class="sxs-lookup"><span data-stu-id="7c85e-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="7c85e-189">Následující ukázkový dokument opravy `CustomerName` nastaví `Orders[0]`hodnotu `Order` a nahradí novým objektem:</span><span class="sxs-lookup"><span data-stu-id="7c85e-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="7c85e-190">Operace přesunu</span><span class="sxs-lookup"><span data-stu-id="7c85e-190">The move operation</span></span>

* <span data-ttu-id="7c85e-191">Pokud `path` odkazuje na prvek `from` pole: zkopíruje prvek do umístění `path` prvku, pak spustí `remove` operaci na `from` prvek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="7c85e-192">Pokud `path` odkazuje na vlastnost: `from` zkopíruje `path` hodnotu vlastnosti vlastnosti, spustí `remove` operaci vlastnosti. `from`</span><span class="sxs-lookup"><span data-stu-id="7c85e-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="7c85e-193">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7c85e-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="7c85e-194">Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="7c85e-195">Pokud prostředek k opravě je `from` dynamický objekt: zkopíruje vlastnost do umístění `path`označeného , pak spustí `remove` operaci na `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="7c85e-196">Následující ukázkový dokument o opravě:</span><span class="sxs-lookup"><span data-stu-id="7c85e-196">The following sample patch document:</span></span>

* <span data-ttu-id="7c85e-197">Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`</span><span class="sxs-lookup"><span data-stu-id="7c85e-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="7c85e-198">Nastaví `Orders[0].OrderName` hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="7c85e-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="7c85e-199">Přesune `Orders[1]` se `Orders[0]`do before .</span><span class="sxs-lookup"><span data-stu-id="7c85e-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="7c85e-200">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="7c85e-200">The copy operation</span></span>

<span data-ttu-id="7c85e-201">Tato operace je funkčně `move` stejná `remove` jako operace bez posledního kroku.</span><span class="sxs-lookup"><span data-stu-id="7c85e-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="7c85e-202">Následující ukázkový dokument o opravě:</span><span class="sxs-lookup"><span data-stu-id="7c85e-202">The following sample patch document:</span></span>

* <span data-ttu-id="7c85e-203">Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`</span><span class="sxs-lookup"><span data-stu-id="7c85e-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="7c85e-204">Vloží kopii `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="7c85e-205">Zkušební operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-205">The test operation</span></span>

<span data-ttu-id="7c85e-206">Pokud se hodnota v umístění `path` označeném v písmenu `value`a) liší od hodnoty uvedené v písmenu , požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="7c85e-207">V takovém případě se nezdaří celý požadavek PATCH i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.</span><span class="sxs-lookup"><span data-stu-id="7c85e-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="7c85e-208">Operace `test` se běžně používá k zabránění aktualizaci, když dojde ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="7c85e-209">Následující ukázkový dokument opravy nemá žádný `CustomerName` vliv, pokud počáteční hodnota je "Jan", protože test se nezdaří:</span><span class="sxs-lookup"><span data-stu-id="7c85e-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="7c85e-210">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="7c85e-210">Get the code</span></span>

<span data-ttu-id="7c85e-211">[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="7c85e-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="7c85e-212">([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7c85e-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7c85e-213">Chcete-li vzorek otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="7c85e-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="7c85e-214">Adresu url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="7c85e-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="7c85e-215">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="7c85e-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="7c85e-216">Záhlaví:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="7c85e-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="7c85e-217">Tělo: Zkopírujte a vložte jeden z vzorků dokumentu opravy JSON ze složky projektu *JSON.*</span><span class="sxs-lookup"><span data-stu-id="7c85e-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c85e-218">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7c85e-218">Additional resources</span></span>

* [<span data-ttu-id="7c85e-219">Specifikace metody IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="7c85e-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="7c85e-220">Specifikace opravy IETF RFC 6902 JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="7c85e-221">Specifikace formátu cesty záplaty IETF RFC 6901 JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="7c85e-222">[Dokumentace k záplatě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="7c85e-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="7c85e-223">Obsahuje odkazy na prostředky pro vytváření dokumentů opravy JSON.</span><span class="sxs-lookup"><span data-stu-id="7c85e-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="7c85e-224">zdrojový kód opravy ASP.NET Jádra JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c85e-225">Tento článek vysvětluje, jak zpracovat požadavky na opravu JSON v ASP.NET základní webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7c85e-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="7c85e-226">Metoda požadavku PATCH HTTP</span><span class="sxs-lookup"><span data-stu-id="7c85e-226">PATCH HTTP request method</span></span>

<span data-ttu-id="7c85e-227">Metody PUT a [PATCH](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="7c85e-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="7c85e-228">Rozdíl mezi nimi je, že PUT nahradí celý prostředek, zatímco PATCH určuje pouze změny.</span><span class="sxs-lookup"><span data-stu-id="7c85e-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="7c85e-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="7c85e-229">JSON Patch</span></span>

<span data-ttu-id="7c85e-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které mají být použity pro prostředek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="7c85e-231">Dokument Záplaty JSON má řadu *operací*.</span><span class="sxs-lookup"><span data-stu-id="7c85e-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="7c85e-232">Každá operace identifikuje určitý typ změny, například přidat prvek pole nebo nahradit hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="7c85e-233">Například následující dokumenty JSON představují prostředek, dokument opravy JSON pro prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="7c85e-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="7c85e-234">Příklad zdroje</span><span class="sxs-lookup"><span data-stu-id="7c85e-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="7c85e-235">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="7c85e-236">V předchozím JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-236">In the preceding JSON:</span></span>

* <span data-ttu-id="7c85e-237">Vlastnost `op` označuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="7c85e-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="7c85e-238">Vlastnost `path` označuje prvek aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="7c85e-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="7c85e-239">Vlastnost `value` poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="7c85e-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="7c85e-240">Zdroj po opravě</span><span class="sxs-lookup"><span data-stu-id="7c85e-240">Resource after patch</span></span>

<span data-ttu-id="7c85e-241">Zde je zdroj po použití předchozího dokumentu Opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="7c85e-242">Změny provedené použitím dokumentu Záplaty JSON na prostředek jsou atomické: pokud se nezdaří jakákoli operace v seznamu, nebude použita žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="7c85e-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="7c85e-243">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="7c85e-243">Path syntax</span></span>

<span data-ttu-id="7c85e-244">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="7c85e-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="7c85e-245">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="7c85e-246">Indexy založené na nule se používají k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="7c85e-247">První prvek `addresses` pole by se `/addresses/0`na .</span><span class="sxs-lookup"><span data-stu-id="7c85e-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="7c85e-248">Na `add` konec pole použijte pomlčku (-) místo čísla indexu: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="7c85e-249">Operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-249">Operations</span></span>

<span data-ttu-id="7c85e-250">V následující tabulce jsou uvedeny podporované operace definované ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="7c85e-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="7c85e-251">Operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-251">Operation</span></span>  | <span data-ttu-id="7c85e-252">Poznámky</span><span class="sxs-lookup"><span data-stu-id="7c85e-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="7c85e-253">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-253">Add a property or array element.</span></span> <span data-ttu-id="7c85e-254">Pro existující vlastnost: set value.</span><span class="sxs-lookup"><span data-stu-id="7c85e-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="7c85e-255">Odeberte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="7c85e-256">Stejné `remove` jako `add` na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="7c85e-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="7c85e-257">Stejné `remove` jako ze `add` zdroje následuje cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="7c85e-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="7c85e-258">Stejné `add` jako k cíli pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="7c85e-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="7c85e-259">Vrátit kód stavu úspěchu, pokud hodnota na `path` = za předpokladu `value`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="7c85e-260">JsonPatch v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="7c85e-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="7c85e-261">ASP.NET Core implementace JSON Patch je k dispozici v balíčku [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="7c85e-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="7c85e-262">Balíček je součástí metabalíčku [Microsoft.AspnetCore.App.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="7c85e-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="7c85e-263">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="7c85e-263">Action method code</span></span>

<span data-ttu-id="7c85e-264">V řadiči rozhraní API metoda akce pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="7c85e-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="7c85e-265">Je anotován `HttpPatch` s atributem.</span><span class="sxs-lookup"><span data-stu-id="7c85e-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="7c85e-266">Přijímá `JsonPatchDocument<T>`, obvykle s `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="7c85e-267">Zavolá `ApplyTo` dokument opravy použít změny.</span><span class="sxs-lookup"><span data-stu-id="7c85e-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="7c85e-268">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="7c85e-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="7c85e-269">Tento kód z ukázkové aplikace `Customer` pracuje s následujícím modelem.</span><span class="sxs-lookup"><span data-stu-id="7c85e-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="7c85e-270">Ukázková metoda akce:</span><span class="sxs-lookup"><span data-stu-id="7c85e-270">The sample action method:</span></span>

* <span data-ttu-id="7c85e-271">Vytvoří `Customer`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="7c85e-272">Aplikuje náplast.</span><span class="sxs-lookup"><span data-stu-id="7c85e-272">Applies the patch.</span></span>
* <span data-ttu-id="7c85e-273">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7c85e-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="7c85e-274">V reálné aplikaci by kód načetl data z úložiště, jako je například databáze, a po použití opravy databázi aktualizoval.</span><span class="sxs-lookup"><span data-stu-id="7c85e-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="7c85e-275">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="7c85e-275">Model state</span></span>

<span data-ttu-id="7c85e-276">Příklad předchozí metody akce volá `ApplyTo` přetížení, které trvá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="7c85e-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="7c85e-277">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="7c85e-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="7c85e-278">Následující příklad ukazuje tělo 400 chybný požadavek `test` odpověď pro operaci:</span><span class="sxs-lookup"><span data-stu-id="7c85e-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="7c85e-279">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="7c85e-279">Dynamic objects</span></span>

<span data-ttu-id="7c85e-280">Následující příklad metody akce ukazuje, jak aplikovat opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="7c85e-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="7c85e-281">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="7c85e-281">The add operation</span></span>

* <span data-ttu-id="7c85e-282">Pokud `path` odkazuje na prvek pole: vloží nový prvek `path`před ten, který je určen .</span><span class="sxs-lookup"><span data-stu-id="7c85e-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="7c85e-283">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="7c85e-284">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="7c85e-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="7c85e-285">Pokud prostředek k opravě je dynamický objekt: přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="7c85e-286">Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="7c85e-287">Následující ukázkový dokument opravy `CustomerName` nastaví `Order` hodnotu a přidá `Orders` objekt na konec pole.</span><span class="sxs-lookup"><span data-stu-id="7c85e-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="7c85e-288">Operace odebrání</span><span class="sxs-lookup"><span data-stu-id="7c85e-288">The remove operation</span></span>

* <span data-ttu-id="7c85e-289">Pokud `path` odkazuje na prvek pole: odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="7c85e-290">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7c85e-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="7c85e-291">Pokud prostředek k opravě je dynamický objekt: odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="7c85e-292">Pokud je prostředek k opravě statický objekt:</span><span class="sxs-lookup"><span data-stu-id="7c85e-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="7c85e-293">Pokud je vlastnost nullable: nastaví ji na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="7c85e-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="7c85e-294">Pokud je vlastnost nenulovatelná, `default<T>`nastaví ji na .</span><span class="sxs-lookup"><span data-stu-id="7c85e-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="7c85e-295">Následující ukázkový dokument `CustomerName` opravy se `Orders[0]`nastaví na hodnotu null a odstraní .</span><span class="sxs-lookup"><span data-stu-id="7c85e-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="7c85e-296">Operace výměny</span><span class="sxs-lookup"><span data-stu-id="7c85e-296">The replace operation</span></span>

<span data-ttu-id="7c85e-297">Tato operace je funkčně `remove` stejná `add`jako následuje .</span><span class="sxs-lookup"><span data-stu-id="7c85e-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="7c85e-298">Následující ukázkový dokument opravy `CustomerName` nastaví `Orders[0]`hodnotu `Order` a nahradí novým objektem.</span><span class="sxs-lookup"><span data-stu-id="7c85e-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="7c85e-299">Operace přesunu</span><span class="sxs-lookup"><span data-stu-id="7c85e-299">The move operation</span></span>

* <span data-ttu-id="7c85e-300">Pokud `path` odkazuje na prvek `from` pole: zkopíruje prvek do umístění `path` prvku, pak spustí `remove` operaci na `from` prvek.</span><span class="sxs-lookup"><span data-stu-id="7c85e-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="7c85e-301">Pokud `path` odkazuje na vlastnost: `from` zkopíruje `path` hodnotu vlastnosti vlastnosti, spustí `remove` operaci vlastnosti. `from`</span><span class="sxs-lookup"><span data-stu-id="7c85e-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="7c85e-302">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7c85e-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="7c85e-303">Pokud prostředek k opravě je statický objekt: požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="7c85e-304">Pokud prostředek k opravě je `from` dynamický objekt: zkopíruje vlastnost do umístění `path`označeného , pak spustí `remove` operaci na `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7c85e-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="7c85e-305">Následující ukázkový dokument o opravě:</span><span class="sxs-lookup"><span data-stu-id="7c85e-305">The following sample patch document:</span></span>

* <span data-ttu-id="7c85e-306">Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`</span><span class="sxs-lookup"><span data-stu-id="7c85e-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="7c85e-307">Nastaví `Orders[0].OrderName` hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="7c85e-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="7c85e-308">Přesune `Orders[1]` se `Orders[0]`do before .</span><span class="sxs-lookup"><span data-stu-id="7c85e-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="7c85e-309">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="7c85e-309">The copy operation</span></span>

<span data-ttu-id="7c85e-310">Tato operace je funkčně `move` stejná `remove` jako operace bez posledního kroku.</span><span class="sxs-lookup"><span data-stu-id="7c85e-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="7c85e-311">Následující ukázkový dokument o opravě:</span><span class="sxs-lookup"><span data-stu-id="7c85e-311">The following sample patch document:</span></span>

* <span data-ttu-id="7c85e-312">Zkopíruje hodnotu `CustomerName`do . `Orders[0].OrderName`</span><span class="sxs-lookup"><span data-stu-id="7c85e-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="7c85e-313">Vloží kopii `Orders[1]` před `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="7c85e-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="7c85e-314">Zkušební operace</span><span class="sxs-lookup"><span data-stu-id="7c85e-314">The test operation</span></span>

<span data-ttu-id="7c85e-315">Pokud se hodnota v umístění `path` označeném v písmenu `value`a) liší od hodnoty uvedené v písmenu , požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c85e-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="7c85e-316">V takovém případě se nezdaří celý požadavek PATCH i v případě, že všechny ostatní operace v dokumentu opravy by jinak úspěšné.</span><span class="sxs-lookup"><span data-stu-id="7c85e-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="7c85e-317">Operace `test` se běžně používá k zabránění aktualizaci, když dojde ke konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="7c85e-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="7c85e-318">Následující ukázkový dokument opravy nemá žádný `CustomerName` vliv, pokud počáteční hodnota je "Jan", protože test se nezdaří:</span><span class="sxs-lookup"><span data-stu-id="7c85e-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="7c85e-319">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="7c85e-319">Get the code</span></span>

<span data-ttu-id="7c85e-320">[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="7c85e-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="7c85e-321">([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="7c85e-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="7c85e-322">Chcete-li vzorek otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="7c85e-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="7c85e-323">Adresu url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="7c85e-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="7c85e-324">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="7c85e-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="7c85e-325">Záhlaví:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="7c85e-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="7c85e-326">Tělo: Zkopírujte a vložte jeden z vzorků dokumentu opravy JSON ze složky projektu *JSON.*</span><span class="sxs-lookup"><span data-stu-id="7c85e-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c85e-327">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7c85e-327">Additional resources</span></span>

* [<span data-ttu-id="7c85e-328">Specifikace metody IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="7c85e-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="7c85e-329">Specifikace opravy IETF RFC 6902 JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="7c85e-330">Specifikace formátu cesty záplaty IETF RFC 6901 JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="7c85e-331">[Dokumentace k záplatě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="7c85e-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="7c85e-332">Obsahuje odkazy na prostředky pro vytváření dokumentů opravy JSON.</span><span class="sxs-lookup"><span data-stu-id="7c85e-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="7c85e-333">zdrojový kód opravy ASP.NET Jádra JSON</span><span class="sxs-lookup"><span data-stu-id="7c85e-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
