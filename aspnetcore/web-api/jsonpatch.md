---
title: JsonPatch v ASP.NET Core Web API
author: rick-anderson
description: Naučte se zpracovávat požadavky na opravy JSON ve ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 09da557d678889ba16abe6f9af40ae1b33583d8b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022365"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="8b03f-103">JsonPatch v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="8b03f-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="8b03f-104">[Dykstra](https://github.com/tdykstra) a [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8b03f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b03f-105">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8b03f-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="8b03f-106">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="8b03f-106">Package installation</span></span>

<span data-ttu-id="8b03f-107">Pokud chcete ve své aplikaci povolit podporu oprav JSON, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="8b03f-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="8b03f-108">Nainstalujte [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="8b03f-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="8b03f-109">Aktualizujte metodu projektu `Startup.ConfigureServices` pro volání <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="8b03f-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="8b03f-110">Například:</span><span class="sxs-lookup"><span data-stu-id="8b03f-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="8b03f-111">`AddNewtonsoftJson`je kompatibilní s metodami registrace služby MVC:</span><span class="sxs-lookup"><span data-stu-id="8b03f-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="8b03f-112">Oprava JSON, AddNewtonsoftJson a System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="8b03f-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="8b03f-113">`AddNewtonsoftJson`nahradí `System.Text.Json` vstupní a výstupní formátovací moduly používané pro formátování **veškerého** obsahu JSON.</span><span class="sxs-lookup"><span data-stu-id="8b03f-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="8b03f-114">Chcete-li přidat podporu pro opravu JSON pomocí příkazu `Newtonsoft.Json` , zatímco ostatní formátovací moduly zůstaly beze změny, aktualizujte `Startup.ConfigureServices` metodu projektu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="8b03f-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="8b03f-115">Předchozí kód vyžaduje `Microsoft.AspNetCore.Mvc.NewtonsoftJson` balíček a následující `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="8b03f-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="8b03f-116">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="8b03f-116">PATCH HTTP request method</span></span>

<span data-ttu-id="8b03f-117">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="8b03f-118">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="8b03f-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="8b03f-119">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-119">JSON Patch</span></span>

<span data-ttu-id="8b03f-120">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="8b03f-121">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="8b03f-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="8b03f-122">Každá operace identifikuje konkrétní typ změny.</span><span class="sxs-lookup"><span data-stu-id="8b03f-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="8b03f-123">Příklady takových změn zahrnují přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="8b03f-124">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="8b03f-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="8b03f-125">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="8b03f-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="8b03f-126">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="8b03f-127">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-127">In the preceding JSON:</span></span>

* <span data-ttu-id="8b03f-128">`op`Vlastnost určuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="8b03f-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="8b03f-129">`path`Vlastnost určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="8b03f-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="8b03f-130">`value`Vlastnost poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="8b03f-131">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="8b03f-131">Resource after patch</span></span>

<span data-ttu-id="8b03f-132">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="8b03f-133">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické.</span><span class="sxs-lookup"><span data-stu-id="8b03f-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="8b03f-134">Pokud se některá operace v seznamu nezdařila, nebude použita žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="8b03f-135">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="8b03f-135">Path syntax</span></span>

<span data-ttu-id="8b03f-136">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="8b03f-137">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="8b03f-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="8b03f-138">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="8b03f-139">První prvek `addresses` pole by byl na `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="8b03f-140">Na `add` konec pole použijte spojovník ( `-` ) místo čísla indexu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="8b03f-141">Operace</span><span class="sxs-lookup"><span data-stu-id="8b03f-141">Operations</span></span>

<span data-ttu-id="8b03f-142">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="8b03f-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="8b03f-143">Operace</span><span class="sxs-lookup"><span data-stu-id="8b03f-143">Operation</span></span>  | <span data-ttu-id="8b03f-144">Poznámky</span><span class="sxs-lookup"><span data-stu-id="8b03f-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="8b03f-145">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-145">Add a property or array element.</span></span> <span data-ttu-id="8b03f-146">Pro existující vlastnost: nastavte hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="8b03f-147">Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="8b03f-148">Stejné jako `remove` následováno ve `add` stejném umístění.</span><span class="sxs-lookup"><span data-stu-id="8b03f-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="8b03f-149">Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b03f-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="8b03f-150">Stejné jako `add` cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b03f-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="8b03f-151">Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="8b03f-152">Oprava JSON v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b03f-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="8b03f-153">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft.AspNetCore.Jspropatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="8b03f-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="8b03f-154">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="8b03f-154">Action method code</span></span>

<span data-ttu-id="8b03f-155">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="8b03f-156">Je označen `HttpPatch` atributem.</span><span class="sxs-lookup"><span data-stu-id="8b03f-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="8b03f-157">Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="8b03f-158">`ApplyTo`Změny se projeví v dokumentu opravy.</span><span class="sxs-lookup"><span data-stu-id="8b03f-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="8b03f-159">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b03f-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="8b03f-160">Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem:</span><span class="sxs-lookup"><span data-stu-id="8b03f-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="8b03f-161">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="8b03f-161">The sample action method:</span></span>

* <span data-ttu-id="8b03f-162">Vytvoří `Customer` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="8b03f-163">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-163">Applies the patch.</span></span>
* <span data-ttu-id="8b03f-164">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="8b03f-165">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="8b03f-166">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="8b03f-166">Model state</span></span>

<span data-ttu-id="8b03f-167">Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="8b03f-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="8b03f-168">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="8b03f-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="8b03f-169">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:</span><span class="sxs-lookup"><span data-stu-id="8b03f-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="8b03f-170">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="8b03f-170">Dynamic objects</span></span>

<span data-ttu-id="8b03f-171">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt:</span><span class="sxs-lookup"><span data-stu-id="8b03f-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="8b03f-172">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="8b03f-172">The add operation</span></span>

* <span data-ttu-id="8b03f-173">`path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="8b03f-174">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="8b03f-175">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="8b03f-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="8b03f-176">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="8b03f-177">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="8b03f-178">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="8b03f-179">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="8b03f-179">The remove operation</span></span>

* <span data-ttu-id="8b03f-180">Pokud `path` odkazuje na element pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="8b03f-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="8b03f-181">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b03f-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="8b03f-182">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="8b03f-183">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="8b03f-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="8b03f-184">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="8b03f-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="8b03f-185">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="8b03f-186">Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="8b03f-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="8b03f-187">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="8b03f-187">The replace operation</span></span>

<span data-ttu-id="8b03f-188">Tato operace je funkčně stejná jako `remove` následováno `add` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="8b03f-189">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem:</span><span class="sxs-lookup"><span data-stu-id="8b03f-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="8b03f-190">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="8b03f-190">The move operation</span></span>

* <span data-ttu-id="8b03f-191">Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="8b03f-192">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="8b03f-193">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b03f-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="8b03f-194">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="8b03f-195">Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="8b03f-196">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="8b03f-196">The following sample patch document:</span></span>

* <span data-ttu-id="8b03f-197">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8b03f-198">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="8b03f-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="8b03f-199">Přesune `Orders[1]` se na dřív `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="8b03f-200">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="8b03f-200">The copy operation</span></span>

<span data-ttu-id="8b03f-201">Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="8b03f-202">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="8b03f-202">The following sample patch document:</span></span>

* <span data-ttu-id="8b03f-203">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8b03f-204">Vloží kopii `Orders[1]` před `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="8b03f-205">Operace testu</span><span class="sxs-lookup"><span data-stu-id="8b03f-205">The test operation</span></span>

<span data-ttu-id="8b03f-206">Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="8b03f-207">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="8b03f-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="8b03f-208">Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="8b03f-209">Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="8b03f-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="8b03f-210">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="8b03f-210">Get the code</span></span>

<span data-ttu-id="8b03f-211">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="8b03f-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="8b03f-212">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b03f-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8b03f-213">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="8b03f-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="8b03f-214">Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="8b03f-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="8b03f-215">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="8b03f-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="8b03f-216">Hlaviček`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="8b03f-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="8b03f-217">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8b03f-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b03f-218">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b03f-218">Additional resources</span></span>

* [<span data-ttu-id="8b03f-219">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="8b03f-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="8b03f-220">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="8b03f-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="8b03f-221">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="8b03f-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="8b03f-222">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="8b03f-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="8b03f-223">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="8b03f-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="8b03f-224">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b03f-225">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="8b03f-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="8b03f-226">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="8b03f-226">PATCH HTTP request method</span></span>

<span data-ttu-id="8b03f-227">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="8b03f-228">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="8b03f-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="8b03f-229">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-229">JSON Patch</span></span>

<span data-ttu-id="8b03f-230">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="8b03f-231">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="8b03f-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="8b03f-232">Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="8b03f-233">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="8b03f-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="8b03f-234">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="8b03f-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="8b03f-235">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="8b03f-236">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-236">In the preceding JSON:</span></span>

* <span data-ttu-id="8b03f-237">`op`Vlastnost určuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="8b03f-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="8b03f-238">`path`Vlastnost určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="8b03f-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="8b03f-239">`value`Vlastnost poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="8b03f-240">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="8b03f-240">Resource after patch</span></span>

<span data-ttu-id="8b03f-241">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="8b03f-242">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="8b03f-243">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="8b03f-243">Path syntax</span></span>

<span data-ttu-id="8b03f-244">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="8b03f-245">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="8b03f-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="8b03f-246">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="8b03f-247">První prvek `addresses` pole by byl na `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="8b03f-248">Na `add` konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="8b03f-249">Operace</span><span class="sxs-lookup"><span data-stu-id="8b03f-249">Operations</span></span>

<span data-ttu-id="8b03f-250">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="8b03f-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="8b03f-251">Operace</span><span class="sxs-lookup"><span data-stu-id="8b03f-251">Operation</span></span>  | <span data-ttu-id="8b03f-252">Poznámky</span><span class="sxs-lookup"><span data-stu-id="8b03f-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="8b03f-253">Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-253">Add a property or array element.</span></span> <span data-ttu-id="8b03f-254">Pro existující vlastnost: nastavte hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="8b03f-255">Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="8b03f-256">Stejné jako `remove` následováno ve `add` stejném umístění.</span><span class="sxs-lookup"><span data-stu-id="8b03f-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="8b03f-257">Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b03f-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="8b03f-258">Stejné jako `add` cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b03f-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="8b03f-259">Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="8b03f-260">JsonPatch v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b03f-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="8b03f-261">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft.AspNetCore.Jspropatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="8b03f-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="8b03f-262">Balíček obsahuje [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) Metapackage.</span><span class="sxs-lookup"><span data-stu-id="8b03f-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="8b03f-263">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="8b03f-263">Action method code</span></span>

<span data-ttu-id="8b03f-264">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="8b03f-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="8b03f-265">Je označen `HttpPatch` atributem.</span><span class="sxs-lookup"><span data-stu-id="8b03f-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="8b03f-266">Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="8b03f-267">`ApplyTo`Změny se projeví v dokumentu opravy.</span><span class="sxs-lookup"><span data-stu-id="8b03f-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="8b03f-268">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b03f-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="8b03f-269">Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem.</span><span class="sxs-lookup"><span data-stu-id="8b03f-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="8b03f-270">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="8b03f-270">The sample action method:</span></span>

* <span data-ttu-id="8b03f-271">Vytvoří `Customer` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="8b03f-272">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-272">Applies the patch.</span></span>
* <span data-ttu-id="8b03f-273">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="8b03f-274">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="8b03f-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="8b03f-275">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="8b03f-275">Model state</span></span>

<span data-ttu-id="8b03f-276">Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="8b03f-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="8b03f-277">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="8b03f-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="8b03f-278">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:</span><span class="sxs-lookup"><span data-stu-id="8b03f-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="8b03f-279">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="8b03f-279">Dynamic objects</span></span>

<span data-ttu-id="8b03f-280">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="8b03f-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="8b03f-281">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="8b03f-281">The add operation</span></span>

* <span data-ttu-id="8b03f-282">`path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="8b03f-283">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="8b03f-284">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="8b03f-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="8b03f-285">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="8b03f-286">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="8b03f-287">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.</span><span class="sxs-lookup"><span data-stu-id="8b03f-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="8b03f-288">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="8b03f-288">The remove operation</span></span>

* <span data-ttu-id="8b03f-289">Pokud `path` odkazuje na element pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="8b03f-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="8b03f-290">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b03f-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="8b03f-291">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="8b03f-292">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="8b03f-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="8b03f-293">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="8b03f-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="8b03f-294">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="8b03f-295">Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="8b03f-296">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="8b03f-296">The replace operation</span></span>

<span data-ttu-id="8b03f-297">Tato operace je funkčně stejná jako `remove` následováno `add` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="8b03f-298">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem.</span><span class="sxs-lookup"><span data-stu-id="8b03f-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="8b03f-299">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="8b03f-299">The move operation</span></span>

* <span data-ttu-id="8b03f-300">Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="8b03f-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="8b03f-301">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="8b03f-302">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b03f-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="8b03f-303">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="8b03f-304">Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b03f-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="8b03f-305">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="8b03f-305">The following sample patch document:</span></span>

* <span data-ttu-id="8b03f-306">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8b03f-307">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="8b03f-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="8b03f-308">Přesune `Orders[1]` se na dřív `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="8b03f-309">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="8b03f-309">The copy operation</span></span>

<span data-ttu-id="8b03f-310">Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.</span><span class="sxs-lookup"><span data-stu-id="8b03f-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="8b03f-311">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="8b03f-311">The following sample patch document:</span></span>

* <span data-ttu-id="8b03f-312">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="8b03f-313">Vloží kopii `Orders[1]` před `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="8b03f-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="8b03f-314">Operace testu</span><span class="sxs-lookup"><span data-stu-id="8b03f-314">The test operation</span></span>

<span data-ttu-id="8b03f-315">Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="8b03f-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="8b03f-316">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="8b03f-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="8b03f-317">Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="8b03f-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="8b03f-318">Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="8b03f-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="8b03f-319">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="8b03f-319">Get the code</span></span>

<span data-ttu-id="8b03f-320">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="8b03f-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="8b03f-321">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b03f-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8b03f-322">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="8b03f-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="8b03f-323">Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="8b03f-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="8b03f-324">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="8b03f-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="8b03f-325">Hlaviček`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="8b03f-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="8b03f-326">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="8b03f-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b03f-327">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b03f-327">Additional resources</span></span>

* [<span data-ttu-id="8b03f-328">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="8b03f-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="8b03f-329">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="8b03f-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="8b03f-330">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="8b03f-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="8b03f-331">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="8b03f-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="8b03f-332">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="8b03f-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="8b03f-333">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="8b03f-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
