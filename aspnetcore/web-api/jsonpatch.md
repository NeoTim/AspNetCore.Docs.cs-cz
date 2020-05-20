---
<span data-ttu-id="f5158-101">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-102">'Blazor'</span></span>
- <span data-ttu-id="f5158-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-103">'Identity'</span></span>
- <span data-ttu-id="f5158-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-105">'Razor'</span></span>
- <span data-ttu-id="f5158-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="f5158-107">JsonPatch v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="f5158-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="f5158-108">[Dykstra](https://github.com/tdykstra) a [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f5158-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5158-109">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f5158-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="f5158-110">Instalace balíčku</span><span class="sxs-lookup"><span data-stu-id="f5158-110">Package installation</span></span>

<span data-ttu-id="f5158-111">Pokud chcete ve své aplikaci povolit podporu oprav JSON, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="f5158-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="f5158-112">Nainstalujte [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f5158-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="f5158-113">Aktualizujte metodu projektu `Startup.ConfigureServices` pro volání <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="f5158-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="f5158-114">Například:</span><span class="sxs-lookup"><span data-stu-id="f5158-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="f5158-115">`AddNewtonsoftJson`je kompatibilní s metodami registrace služby MVC:</span><span class="sxs-lookup"><span data-stu-id="f5158-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="f5158-116">JSON patch, AddNewtonsoftJson a System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="f5158-117">`AddNewtonsoftJson`nahradí `System.Text.Json` vstupní a výstupní formátovací moduly používané pro formátování **veškerého** obsahu JSON.</span><span class="sxs-lookup"><span data-stu-id="f5158-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="f5158-118">Chcete-li přidat podporu pro opravu JSON pomocí příkazu `Newtonsoft.Json` , zatímco ostatní formátovací moduly zůstaly beze změny, aktualizujte `Startup.ConfigureServices` metodu projektu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f5158-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="f5158-119">Předchozí kód vyžaduje `Microsoft.AspNetCore.Mvc.NewtonsoftJson` balíček a následující `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="f5158-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="f5158-120">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="f5158-120">PATCH HTTP request method</span></span>

<span data-ttu-id="f5158-121">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="f5158-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f5158-122">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="f5158-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f5158-123">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-123">JSON Patch</span></span>

<span data-ttu-id="f5158-124">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="f5158-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f5158-125">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="f5158-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f5158-126">Každá operace identifikuje konkrétní typ změny.</span><span class="sxs-lookup"><span data-stu-id="f5158-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="f5158-127">Příklady takových změn zahrnují přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="f5158-128">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="f5158-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f5158-129">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="f5158-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f5158-130">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f5158-131">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-131">In the preceding JSON:</span></span>

* <span data-ttu-id="f5158-132">`op`Vlastnost určuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="f5158-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f5158-133">`path`Vlastnost určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="f5158-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f5158-134">`value`Vlastnost poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f5158-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f5158-135">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="f5158-135">Resource after patch</span></span>

<span data-ttu-id="f5158-136">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="f5158-137">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické.</span><span class="sxs-lookup"><span data-stu-id="f5158-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="f5158-138">Pokud se některá operace v seznamu nezdařila, nebude použita žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="f5158-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f5158-139">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="f5158-139">Path syntax</span></span>

<span data-ttu-id="f5158-140">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="f5158-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f5158-141">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="f5158-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f5158-142">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f5158-143">První prvek `addresses` pole by byl na `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="f5158-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f5158-144">Na `add` konec pole použijte spojovník ( `-` ) místo čísla indexu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="f5158-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f5158-145">Operace</span><span class="sxs-lookup"><span data-stu-id="f5158-145">Operations</span></span>

<span data-ttu-id="f5158-146">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="f5158-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f5158-147">Operace</span><span class="sxs-lookup"><span data-stu-id="f5158-147">Operation</span></span>  | <span data-ttu-id="f5158-148">Poznámky</span><span class="sxs-lookup"><span data-stu-id="f5158-148">Notes</span></span> |
|---
<span data-ttu-id="f5158-149">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-150">'Blazor'</span></span>
- <span data-ttu-id="f5158-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-151">'Identity'</span></span>
- <span data-ttu-id="f5158-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-153">'Razor'</span></span>
- <span data-ttu-id="f5158-154">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-155">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-156">'Blazor'</span></span>
- <span data-ttu-id="f5158-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-157">'Identity'</span></span>
- <span data-ttu-id="f5158-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-159">'Razor'</span></span>
- <span data-ttu-id="f5158-160">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-161">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-162">'Blazor'</span></span>
- <span data-ttu-id="f5158-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-163">'Identity'</span></span>
- <span data-ttu-id="f5158-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-165">'Razor'</span></span>
- <span data-ttu-id="f5158-166">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-166">'SignalR' uid:</span></span> 

<span data-ttu-id="f5158-167">------|---
Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-168">'Blazor'</span></span>
- <span data-ttu-id="f5158-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-169">'Identity'</span></span>
- <span data-ttu-id="f5158-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-171">'Razor'</span></span>
- <span data-ttu-id="f5158-172">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-173">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-174">'Blazor'</span></span>
- <span data-ttu-id="f5158-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-175">'Identity'</span></span>
- <span data-ttu-id="f5158-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-177">'Razor'</span></span>
- <span data-ttu-id="f5158-178">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-179">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-180">'Blazor'</span></span>
- <span data-ttu-id="f5158-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-181">'Identity'</span></span>
- <span data-ttu-id="f5158-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-183">'Razor'</span></span>
- <span data-ttu-id="f5158-184">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-185">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-186">'Blazor'</span></span>
- <span data-ttu-id="f5158-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-187">'Identity'</span></span>
- <span data-ttu-id="f5158-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-189">'Razor'</span></span>
- <span data-ttu-id="f5158-190">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-191">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-192">'Blazor'</span></span>
- <span data-ttu-id="f5158-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-193">'Identity'</span></span>
- <span data-ttu-id="f5158-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-195">'Razor'</span></span>
- <span data-ttu-id="f5158-196">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-197">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-198">'Blazor'</span></span>
- <span data-ttu-id="f5158-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-199">'Identity'</span></span>
- <span data-ttu-id="f5158-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-201">'Razor'</span></span>
- <span data-ttu-id="f5158-202">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-203">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-204">'Blazor'</span></span>
- <span data-ttu-id="f5158-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-205">'Identity'</span></span>
- <span data-ttu-id="f5158-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-207">'Razor'</span></span>
- <span data-ttu-id="f5158-208">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-209">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-210">'Blazor'</span></span>
- <span data-ttu-id="f5158-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-211">'Identity'</span></span>
- <span data-ttu-id="f5158-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-213">'Razor'</span></span>
- <span data-ttu-id="f5158-214">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-215">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-216">'Blazor'</span></span>
- <span data-ttu-id="f5158-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-217">'Identity'</span></span>
- <span data-ttu-id="f5158-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-219">'Razor'</span></span>
- <span data-ttu-id="f5158-220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-221">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-222">'Blazor'</span></span>
- <span data-ttu-id="f5158-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-223">'Identity'</span></span>
- <span data-ttu-id="f5158-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-225">'Razor'</span></span>
- <span data-ttu-id="f5158-226">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-227">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-228">'Blazor'</span></span>
- <span data-ttu-id="f5158-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-229">'Identity'</span></span>
- <span data-ttu-id="f5158-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-231">'Razor'</span></span>
- <span data-ttu-id="f5158-232">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-233">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-234">'Blazor'</span></span>
- <span data-ttu-id="f5158-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-235">'Identity'</span></span>
- <span data-ttu-id="f5158-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-237">'Razor'</span></span>
- <span data-ttu-id="f5158-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-239">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-240">'Blazor'</span></span>
- <span data-ttu-id="f5158-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-241">'Identity'</span></span>
- <span data-ttu-id="f5158-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-243">'Razor'</span></span>
- <span data-ttu-id="f5158-244">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-245">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-246">'Blazor'</span></span>
- <span data-ttu-id="f5158-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-247">'Identity'</span></span>
- <span data-ttu-id="f5158-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-249">'Razor'</span></span>
- <span data-ttu-id="f5158-250">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-250">'SignalR' uid:</span></span> 

<span data-ttu-id="f5158-251">----------------| | `add`     | Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="f5158-252">Pro existující vlastnost: nastavte hodnotu. | | `remove`  | Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="f5158-253">| | `replace` | Stejné jako `remove` následováno ve `add` stejném umístění.</span><span class="sxs-lookup"><span data-stu-id="f5158-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="f5158-254">| | `move`    | Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f5158-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="f5158-255">| | `copy`    | Stejné jako `add` cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f5158-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="f5158-256">| | `test`    | Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` . |</span><span class="sxs-lookup"><span data-stu-id="f5158-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="f5158-257">Oprava JSON v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5158-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="f5158-258">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="f5158-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f5158-259">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="f5158-259">Action method code</span></span>

<span data-ttu-id="f5158-260">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f5158-261">Je označen `HttpPatch` atributem.</span><span class="sxs-lookup"><span data-stu-id="f5158-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f5158-262">Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="f5158-263">`ApplyTo`Změny se projeví v dokumentu opravy.</span><span class="sxs-lookup"><span data-stu-id="f5158-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f5158-264">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f5158-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f5158-265">Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem:</span><span class="sxs-lookup"><span data-stu-id="f5158-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f5158-266">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="f5158-266">The sample action method:</span></span>

* <span data-ttu-id="f5158-267">Vytvoří `Customer` .</span><span class="sxs-lookup"><span data-stu-id="f5158-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f5158-268">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="f5158-268">Applies the patch.</span></span>
* <span data-ttu-id="f5158-269">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f5158-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="f5158-270">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="f5158-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f5158-271">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="f5158-271">Model state</span></span>

<span data-ttu-id="f5158-272">Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="f5158-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f5158-273">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="f5158-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f5158-274">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:</span><span class="sxs-lookup"><span data-stu-id="f5158-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f5158-275">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="f5158-275">Dynamic objects</span></span>

<span data-ttu-id="f5158-276">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt:</span><span class="sxs-lookup"><span data-stu-id="f5158-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f5158-277">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="f5158-277">The add operation</span></span>

* <span data-ttu-id="f5158-278">`path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .</span><span class="sxs-lookup"><span data-stu-id="f5158-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f5158-279">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f5158-280">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="f5158-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f5158-281">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f5158-282">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f5158-283">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f5158-284">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="f5158-284">The remove operation</span></span>

* <span data-ttu-id="f5158-285">Pokud `path` odkazuje na element pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="f5158-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f5158-286">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f5158-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="f5158-287">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f5158-288">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="f5158-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="f5158-289">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="f5158-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f5158-290">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="f5158-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f5158-291">Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="f5158-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f5158-292">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="f5158-292">The replace operation</span></span>

<span data-ttu-id="f5158-293">Tato operace je funkčně stejná jako `remove` následováno `add` .</span><span class="sxs-lookup"><span data-stu-id="f5158-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f5158-294">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem:</span><span class="sxs-lookup"><span data-stu-id="f5158-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f5158-295">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="f5158-295">The move operation</span></span>

* <span data-ttu-id="f5158-296">Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="f5158-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f5158-297">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f5158-298">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f5158-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f5158-299">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f5158-300">Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f5158-301">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="f5158-301">The following sample patch document:</span></span>

* <span data-ttu-id="f5158-302">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="f5158-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f5158-303">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f5158-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f5158-304">Přesune `Orders[1]` se na dřív `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f5158-305">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="f5158-305">The copy operation</span></span>

<span data-ttu-id="f5158-306">Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.</span><span class="sxs-lookup"><span data-stu-id="f5158-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f5158-307">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="f5158-307">The following sample patch document:</span></span>

* <span data-ttu-id="f5158-308">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="f5158-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f5158-309">Vloží kopii `Orders[1]` před `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f5158-310">Operace testu</span><span class="sxs-lookup"><span data-stu-id="f5158-310">The test operation</span></span>

<span data-ttu-id="f5158-311">Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f5158-312">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="f5158-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f5158-313">Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f5158-314">Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="f5158-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f5158-315">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="f5158-315">Get the code</span></span>

<span data-ttu-id="f5158-316">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="f5158-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="f5158-317">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f5158-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f5158-318">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="f5158-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f5158-319">Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f5158-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f5158-320">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="f5158-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f5158-321">Hlaviček`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f5158-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f5158-322">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="f5158-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5158-323">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f5158-323">Additional resources</span></span>

* [<span data-ttu-id="f5158-324">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="f5158-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f5158-325">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="f5158-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f5158-326">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="f5158-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f5158-327">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="f5158-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="f5158-328">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="f5158-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f5158-329">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5158-330">Tento článek vysvětluje, jak zpracovat žádosti o opravu JSON ve ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f5158-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="f5158-331">Oprava metody požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="f5158-331">PATCH HTTP request method</span></span>

<span data-ttu-id="f5158-332">Metody PUT a [patch](https://tools.ietf.org/html/rfc5789) se používají k aktualizaci existujícího prostředku.</span><span class="sxs-lookup"><span data-stu-id="f5158-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f5158-333">Rozdíl mezi nimi spočívá v tom, že PUT nahradí celý prostředek, zatímco PATCH zadává pouze změny.</span><span class="sxs-lookup"><span data-stu-id="f5158-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f5158-334">Oprava JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-334">JSON Patch</span></span>

<span data-ttu-id="f5158-335">[Oprava JSON](https://tools.ietf.org/html/rfc6902) je formát pro určení aktualizací, které se mají použít u prostředku.</span><span class="sxs-lookup"><span data-stu-id="f5158-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f5158-336">Dokument opravy JSON má pole *operací*.</span><span class="sxs-lookup"><span data-stu-id="f5158-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f5158-337">Každá operace identifikuje konkrétní typ změny, jako je například přidání prvku pole nebo nahrazení hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="f5158-338">Následující dokumenty JSON například reprezentují prostředek, dokument opravy JSON pro daný prostředek a výsledek použití operací opravy.</span><span class="sxs-lookup"><span data-stu-id="f5158-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f5158-339">Příklad prostředku</span><span class="sxs-lookup"><span data-stu-id="f5158-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f5158-340">Příklad opravy JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f5158-341">V předchozím kódu JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-341">In the preceding JSON:</span></span>

* <span data-ttu-id="f5158-342">`op`Vlastnost určuje typ operace.</span><span class="sxs-lookup"><span data-stu-id="f5158-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f5158-343">`path`Vlastnost určuje prvek, který se má aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="f5158-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f5158-344">`value`Vlastnost poskytuje novou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f5158-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f5158-345">Prostředek po opravě</span><span class="sxs-lookup"><span data-stu-id="f5158-345">Resource after patch</span></span>

<span data-ttu-id="f5158-346">Toto je prostředek po použití předchozího dokumentu opravy JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="f5158-347">Změny provedené při použití dokumentu opravy JSON na prostředek jsou atomické: Pokud se některá operace v seznamu nezdařila, nepoužije se žádná operace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="f5158-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f5158-348">Syntaxe cesty</span><span class="sxs-lookup"><span data-stu-id="f5158-348">Path syntax</span></span>

<span data-ttu-id="f5158-349">Vlastnost [path](https://tools.ietf.org/html/rfc6901) objektu operace má lomítka mezi úrovněmi.</span><span class="sxs-lookup"><span data-stu-id="f5158-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f5158-350">Například, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="f5158-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f5158-351">Indexy založené na nule slouží k určení prvků pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f5158-352">První prvek `addresses` pole by byl na `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="f5158-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f5158-353">Na `add` konec pole, použijte spojovník (-) místo čísla indexu: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="f5158-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f5158-354">Operace</span><span class="sxs-lookup"><span data-stu-id="f5158-354">Operations</span></span>

<span data-ttu-id="f5158-355">V následující tabulce jsou uvedeny podporované operace, jak je definováno ve [specifikaci opravy JSON](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="f5158-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f5158-356">Operace</span><span class="sxs-lookup"><span data-stu-id="f5158-356">Operation</span></span>  | <span data-ttu-id="f5158-357">Poznámky</span><span class="sxs-lookup"><span data-stu-id="f5158-357">Notes</span></span> |
|---
<span data-ttu-id="f5158-358">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-359">'Blazor'</span></span>
- <span data-ttu-id="f5158-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-360">'Identity'</span></span>
- <span data-ttu-id="f5158-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-362">'Razor'</span></span>
- <span data-ttu-id="f5158-363">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-364">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-365">'Blazor'</span></span>
- <span data-ttu-id="f5158-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-366">'Identity'</span></span>
- <span data-ttu-id="f5158-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-368">'Razor'</span></span>
- <span data-ttu-id="f5158-369">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-370">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-371">'Blazor'</span></span>
- <span data-ttu-id="f5158-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-372">'Identity'</span></span>
- <span data-ttu-id="f5158-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-374">'Razor'</span></span>
- <span data-ttu-id="f5158-375">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-375">'SignalR' uid:</span></span> 

<span data-ttu-id="f5158-376">------|---
Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-377">'Blazor'</span></span>
- <span data-ttu-id="f5158-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-378">'Identity'</span></span>
- <span data-ttu-id="f5158-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-380">'Razor'</span></span>
- <span data-ttu-id="f5158-381">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-382">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-383">'Blazor'</span></span>
- <span data-ttu-id="f5158-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-384">'Identity'</span></span>
- <span data-ttu-id="f5158-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-386">'Razor'</span></span>
- <span data-ttu-id="f5158-387">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-388">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-389">'Blazor'</span></span>
- <span data-ttu-id="f5158-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-390">'Identity'</span></span>
- <span data-ttu-id="f5158-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-392">'Razor'</span></span>
- <span data-ttu-id="f5158-393">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-394">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-395">'Blazor'</span></span>
- <span data-ttu-id="f5158-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-396">'Identity'</span></span>
- <span data-ttu-id="f5158-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-398">'Razor'</span></span>
- <span data-ttu-id="f5158-399">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-400">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-401">'Blazor'</span></span>
- <span data-ttu-id="f5158-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-402">'Identity'</span></span>
- <span data-ttu-id="f5158-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-404">'Razor'</span></span>
- <span data-ttu-id="f5158-405">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-406">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-407">'Blazor'</span></span>
- <span data-ttu-id="f5158-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-408">'Identity'</span></span>
- <span data-ttu-id="f5158-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-410">'Razor'</span></span>
- <span data-ttu-id="f5158-411">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-412">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-413">'Blazor'</span></span>
- <span data-ttu-id="f5158-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-414">'Identity'</span></span>
- <span data-ttu-id="f5158-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-416">'Razor'</span></span>
- <span data-ttu-id="f5158-417">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-418">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-419">'Blazor'</span></span>
- <span data-ttu-id="f5158-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-420">'Identity'</span></span>
- <span data-ttu-id="f5158-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-422">'Razor'</span></span>
- <span data-ttu-id="f5158-423">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-424">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-425">'Blazor'</span></span>
- <span data-ttu-id="f5158-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-426">'Identity'</span></span>
- <span data-ttu-id="f5158-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-428">'Razor'</span></span>
- <span data-ttu-id="f5158-429">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-430">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-431">'Blazor'</span></span>
- <span data-ttu-id="f5158-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-432">'Identity'</span></span>
- <span data-ttu-id="f5158-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-434">'Razor'</span></span>
- <span data-ttu-id="f5158-435">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-436">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-437">'Blazor'</span></span>
- <span data-ttu-id="f5158-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-438">'Identity'</span></span>
- <span data-ttu-id="f5158-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-440">'Razor'</span></span>
- <span data-ttu-id="f5158-441">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-442">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-443">'Blazor'</span></span>
- <span data-ttu-id="f5158-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-444">'Identity'</span></span>
- <span data-ttu-id="f5158-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-446">'Razor'</span></span>
- <span data-ttu-id="f5158-447">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-448">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-449">'Blazor'</span></span>
- <span data-ttu-id="f5158-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-450">'Identity'</span></span>
- <span data-ttu-id="f5158-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-452">'Razor'</span></span>
- <span data-ttu-id="f5158-453">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5158-454">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="f5158-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5158-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5158-455">'Blazor'</span></span>
- <span data-ttu-id="f5158-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5158-456">'Identity'</span></span>
- <span data-ttu-id="f5158-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5158-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5158-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5158-458">'Razor'</span></span>
- <span data-ttu-id="f5158-459">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="f5158-459">'SignalR' uid:</span></span> 

<span data-ttu-id="f5158-460">----------------| | `add`     | Přidejte vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="f5158-461">Pro existující vlastnost: nastavte hodnotu. | | `remove`  | Odebere vlastnost nebo prvek pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="f5158-462">| | `replace` | Stejné jako `remove` následováno ve `add` stejném umístění.</span><span class="sxs-lookup"><span data-stu-id="f5158-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="f5158-463">| | `move`    | Stejné jako `remove` u zdroje, po kterém následuje `add` k cíli, pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f5158-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="f5158-464">| | `copy`    | Stejné jako `add` cíl pomocí hodnoty ze zdroje.</span><span class="sxs-lookup"><span data-stu-id="f5158-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="f5158-465">| | `test`    | Vrátí stavový kód úspěšného zpracování, pokud je zadána hodnota at `path` `value` . |</span><span class="sxs-lookup"><span data-stu-id="f5158-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="f5158-466">JsonPatch v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5158-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="f5158-467">Implementace opravy JSON ASP.NET Core je k dispozici v balíčku NuGet [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) .</span><span class="sxs-lookup"><span data-stu-id="f5158-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="f5158-468">Balíček obsahuje [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) Metapackage.</span><span class="sxs-lookup"><span data-stu-id="f5158-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f5158-469">Kód metody akce</span><span class="sxs-lookup"><span data-stu-id="f5158-469">Action method code</span></span>

<span data-ttu-id="f5158-470">V řadiči rozhraní API metoda Action pro opravu JSON:</span><span class="sxs-lookup"><span data-stu-id="f5158-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f5158-471">Je označen `HttpPatch` atributem.</span><span class="sxs-lookup"><span data-stu-id="f5158-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f5158-472">Akceptuje `JsonPatchDocument<T>` , obvykle s `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="f5158-473">`ApplyTo`Změny se projeví v dokumentu opravy.</span><span class="sxs-lookup"><span data-stu-id="f5158-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f5158-474">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f5158-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f5158-475">Tento kód z ukázkové aplikace funguje s následujícím `Customer` modelem.</span><span class="sxs-lookup"><span data-stu-id="f5158-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f5158-476">Ukázková akce metody:</span><span class="sxs-lookup"><span data-stu-id="f5158-476">The sample action method:</span></span>

* <span data-ttu-id="f5158-477">Vytvoří `Customer` .</span><span class="sxs-lookup"><span data-stu-id="f5158-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f5158-478">Aplikuje opravu.</span><span class="sxs-lookup"><span data-stu-id="f5158-478">Applies the patch.</span></span>
* <span data-ttu-id="f5158-479">Vrátí výsledek v těle odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f5158-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="f5158-480">V reálné aplikaci kód načetl data z úložiště, jako je databáze, a po použití opravy aktualizuje databázi.</span><span class="sxs-lookup"><span data-stu-id="f5158-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f5158-481">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="f5158-481">Model state</span></span>

<span data-ttu-id="f5158-482">Předchozí příklad metody volá přetížení `ApplyTo` , které přijímá stav modelu jako jeden z jeho parametrů.</span><span class="sxs-lookup"><span data-stu-id="f5158-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f5158-483">Pomocí této možnosti můžete získat chybové zprávy v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="f5158-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f5158-484">Následující příklad ukazuje tělo 400 chybné odpovědi žádosti pro `test` operaci:</span><span class="sxs-lookup"><span data-stu-id="f5158-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f5158-485">Dynamické objekty</span><span class="sxs-lookup"><span data-stu-id="f5158-485">Dynamic objects</span></span>

<span data-ttu-id="f5158-486">Následující příklad metody akce ukazuje, jak použít opravu na dynamický objekt.</span><span class="sxs-lookup"><span data-stu-id="f5158-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f5158-487">Operace přidání</span><span class="sxs-lookup"><span data-stu-id="f5158-487">The add operation</span></span>

* <span data-ttu-id="f5158-488">`path`Odkazuje-li na prvek pole: vloží nový prvek před první, který je určen parametrem `path` .</span><span class="sxs-lookup"><span data-stu-id="f5158-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f5158-489">Pokud `path` odkazuje na vlastnost: nastaví hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f5158-490">Pokud `path` odkazuje na neexistující umístění:</span><span class="sxs-lookup"><span data-stu-id="f5158-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f5158-491">Pokud je prostředek k opravě dynamický objekt: Přidá vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f5158-492">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f5158-493">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a přidá `Order` objekt na konec `Orders` pole.</span><span class="sxs-lookup"><span data-stu-id="f5158-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f5158-494">Operace Remove</span><span class="sxs-lookup"><span data-stu-id="f5158-494">The remove operation</span></span>

* <span data-ttu-id="f5158-495">Pokud `path` odkazuje na element pole: Odebere prvek.</span><span class="sxs-lookup"><span data-stu-id="f5158-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f5158-496">Pokud `path` odkazuje na vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f5158-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="f5158-497">Pokud je prostředek k opravě dynamický objekt: Odebere vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f5158-498">Pokud je prostředek pro opravu statickým objektem:</span><span class="sxs-lookup"><span data-stu-id="f5158-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="f5158-499">Pokud je vlastnost Nullable: nastaví ji na null.</span><span class="sxs-lookup"><span data-stu-id="f5158-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f5158-500">Pokud vlastnost nemůže mít hodnotu null, nastaví ji na `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="f5158-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f5158-501">Následující ukázkový dokument opravy se nastaví `CustomerName` na hodnotu null a odstraní `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f5158-502">Operace Replace</span><span class="sxs-lookup"><span data-stu-id="f5158-502">The replace operation</span></span>

<span data-ttu-id="f5158-503">Tato operace je funkčně stejná jako `remove` následováno `add` .</span><span class="sxs-lookup"><span data-stu-id="f5158-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f5158-504">Následující ukázkový dokument opravy nastavuje hodnotu `CustomerName` a nahrazuje `Orders[0]` novým `Order` objektem.</span><span class="sxs-lookup"><span data-stu-id="f5158-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f5158-505">Operace přesunutí</span><span class="sxs-lookup"><span data-stu-id="f5158-505">The move operation</span></span>

* <span data-ttu-id="f5158-506">Pokud `path` odkazuje na element pole: zkopíruje `from` element do umístění `path` elementu a potom spustí `remove` operaci na `from` elementu.</span><span class="sxs-lookup"><span data-stu-id="f5158-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f5158-507">Pokud `path` odkazuje na vlastnost: zkopíruje hodnotu `from` vlastnosti na `path` vlastnost a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f5158-508">Pokud `path` odkazuje na neexistující vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f5158-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f5158-509">Pokud je prostředek k opravě statický objekt: požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f5158-510">Pokud je prostředek pro opravu dynamický objekt: zkopíruje `from` vlastnost do umístění označeného `path` a potom spustí `remove` operaci pro `from` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5158-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f5158-511">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="f5158-511">The following sample patch document:</span></span>

* <span data-ttu-id="f5158-512">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="f5158-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f5158-513">Nastaví `Orders[0].OrderName` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f5158-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f5158-514">Přesune `Orders[1]` se na dřív `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f5158-515">Operace kopírování</span><span class="sxs-lookup"><span data-stu-id="f5158-515">The copy operation</span></span>

<span data-ttu-id="f5158-516">Tato operace je funkčně stejná jako `move` operace bez posledního `remove` kroku.</span><span class="sxs-lookup"><span data-stu-id="f5158-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f5158-517">Následující ukázkový dokument opravy:</span><span class="sxs-lookup"><span data-stu-id="f5158-517">The following sample patch document:</span></span>

* <span data-ttu-id="f5158-518">Zkopíruje hodnotu `Orders[0].OrderName` do `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="f5158-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f5158-519">Vloží kopii `Orders[1]` před `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="f5158-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f5158-520">Operace testu</span><span class="sxs-lookup"><span data-stu-id="f5158-520">The test operation</span></span>

<span data-ttu-id="f5158-521">Pokud hodnota v umístění, která je uvedena v `path` , se liší od hodnoty uvedené v `value` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="f5158-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f5158-522">V takovém případě celá žádost o opravu selže i v případě, že všechny ostatní operace v dokumentu opravy by jinak uspěly.</span><span class="sxs-lookup"><span data-stu-id="f5158-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f5158-523">Tato `test` operace se běžně používá k tomu, aby se zabránilo aktualizaci v případě konfliktu souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="f5158-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f5158-524">Následující ukázkový dokument opravy nemá žádný vliv, pokud je původní hodnota `CustomerName` "Jan", protože test se nezdařil:</span><span class="sxs-lookup"><span data-stu-id="f5158-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f5158-525">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="f5158-525">Get the code</span></span>

<span data-ttu-id="f5158-526">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="f5158-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="f5158-527">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f5158-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f5158-528">Pokud chcete ukázku otestovat, spusťte aplikaci a odešlete požadavky HTTP s následujícím nastavením:</span><span class="sxs-lookup"><span data-stu-id="f5158-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f5158-529">Adresa URL`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f5158-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f5158-530">Metoda HTTP:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="f5158-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f5158-531">Hlaviček`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f5158-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f5158-532">Tělo: Zkopírujte a vložte jeden ze vzorků dokumentů opravy JSON ze složky projektu *JSON* .</span><span class="sxs-lookup"><span data-stu-id="f5158-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5158-533">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f5158-533">Additional resources</span></span>

* [<span data-ttu-id="f5158-534">IETF RFC 5789 – specifikace metody opravy</span><span class="sxs-lookup"><span data-stu-id="f5158-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f5158-535">Specifikace opravy JSON pro IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="f5158-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f5158-536">IETF RFC 6901 JSON – specifikace formátu pro cestu k opravě</span><span class="sxs-lookup"><span data-stu-id="f5158-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f5158-537">[Dokumentace k opravě JSON](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="f5158-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="f5158-538">Obsahuje odkazy na zdroje informací pro vytváření dokumentů oprav JSON.</span><span class="sxs-lookup"><span data-stu-id="f5158-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f5158-539">ASP.NET Core zdrojový kód opravy JSON</span><span class="sxs-lookup"><span data-stu-id="f5158-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
