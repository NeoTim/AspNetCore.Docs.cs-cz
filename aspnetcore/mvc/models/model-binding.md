---
title: Vazby modelu v ASP.NET Core
author: tdykstra
description: Zjistěte, jak vazby modelu v ASP.NET Core funguje a jak můžete přizpůsobit její chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 7d62ccecdacbd34a38a1fd8c58979a9b09cf86e8
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750204"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="5b224-103">Vazby modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b224-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="5b224-104">Tento článek vysvětluje, jaké vazby modelu je, jak to funguje a jak přizpůsobit chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="5b224-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="5b224-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5b224-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="5b224-106">Co je vazby modelu</span><span class="sxs-lookup"><span data-stu-id="5b224-106">What is Model binding</span></span>

<span data-ttu-id="5b224-107">Řadiče a stránky Razor pracovat s daty, která pochází z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b224-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="5b224-108">Například data trasy, která může poskytnout klíč záznamu a pole odeslaného formuláře může poskytnout hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="5b224-109">Zápis kódu pro každou z těchto hodnot načíst a je převést na typy .NET z řetězců může být zdlouhavé a náchylné k chybě.</span><span class="sxs-lookup"><span data-stu-id="5b224-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="5b224-110">Tento proces automatizuje vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-110">Model binding automates this process.</span></span> <span data-ttu-id="5b224-111">Systém vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="5b224-111">The model binding system:</span></span>

* <span data-ttu-id="5b224-112">Načte data z různých zdrojů, jako je směrování dat, pole formuláře a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5b224-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="5b224-113">Poskytuje data, která mají řadiče a stránek Razor parametry metody a veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5b224-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="5b224-114">Převede řetězec data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="5b224-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="5b224-115">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="5b224-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="5b224-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="5b224-116">Example</span></span>

<span data-ttu-id="5b224-117">Předpokládejme, že máte následující metody akce:</span><span class="sxs-lookup"><span data-stu-id="5b224-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="5b224-118">A aplikace obdrží žádost o tuto adresu URL:</span><span class="sxs-lookup"><span data-stu-id="5b224-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="5b224-119">Vazby modelu přejde ale takto po směrování systému vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="5b224-119">Model binding goes though the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="5b224-120">Najde první parametr `GetByID`, celočíselným parametrem s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="5b224-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="5b224-121">Prohlédne dostupné zdroje v požadavku HTTP a vyhledá `id` = "2" v datech trasy.</span><span class="sxs-lookup"><span data-stu-id="5b224-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="5b224-122">Převede řetězec "2" do celého čísla 2.</span><span class="sxs-lookup"><span data-stu-id="5b224-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="5b224-123">Najde další parametr `GetByID`, logickou hodnotu s názvem `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="5b224-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="5b224-124">Prohlédne zdroje a vyhledá "DogsOnly = true" v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="5b224-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="5b224-125">Shoda názvu není malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5b224-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="5b224-126">Převede řetězec "true" na logickou hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="5b224-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="5b224-127">Pak zavolá rozhraní `GetById` metodu 2 pro `id` parametr, a `true` pro `dogsOnly` parametru.</span><span class="sxs-lookup"><span data-stu-id="5b224-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="5b224-128">V předchozím příkladu cíle vazby modelu jsou parametry metody, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="5b224-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="5b224-129">Cíle může být také vlastností komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="5b224-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="5b224-130">Po každé vlastnosti je úspěšně navázán, [ověření modelu](xref:mvc/models/validation) dojde k této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5b224-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="5b224-131">Požadovaný záznam, jaká data je vázán na model a všechny chyby ověření nebo vazba je uložena v [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="5b224-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="5b224-132">Chcete-li zjistit, pokud tento proces byl úspěšný, je aplikace zkontroluje [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) příznak.</span><span class="sxs-lookup"><span data-stu-id="5b224-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="5b224-133">Cíle</span><span class="sxs-lookup"><span data-stu-id="5b224-133">Targets</span></span>

<span data-ttu-id="5b224-134">Vazby modelu se pokusí najít hodnoty pro následující typy cíle:</span><span class="sxs-lookup"><span data-stu-id="5b224-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="5b224-135">Parametry metody akce kontroleru, který požadavek prochází, k.</span><span class="sxs-lookup"><span data-stu-id="5b224-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="5b224-136">Parametry metody obslužné rutiny pro stránky Razor, který požadavek prochází, k.</span><span class="sxs-lookup"><span data-stu-id="5b224-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="5b224-137">Veřejné vlastnosti kontroleru nebo `PageModel` třídy, pokud zadané atributy.</span><span class="sxs-lookup"><span data-stu-id="5b224-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="5b224-138">Atribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="5b224-138">[BindProperty] attribute</span></span>

<span data-ttu-id="5b224-139">Můžete použít pro veřejnou vlastnost řadiče nebo `PageModel` třídy způsobit vazby modelu pro tuto vlastnost cíle:</span><span class="sxs-lookup"><span data-stu-id="5b224-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="5b224-140">Atribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="5b224-140">[BindProperties] attribute</span></span>

<span data-ttu-id="5b224-141">K dispozici v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="5b224-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="5b224-142">Můžete použít k řadiči nebo `PageModel` třídy říct vazby modelu cílit na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="5b224-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="5b224-143">Vazby pro požadavky HTTP GET modelu</span><span class="sxs-lookup"><span data-stu-id="5b224-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="5b224-144">Ve výchozím nastavení nejsou vázané vlastnosti pro požadavky HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5b224-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="5b224-145">Obvykle je vše, co potřebujete pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="5b224-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="5b224-146">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="5b224-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="5b224-147">Proto není nutné vytvořit vazbu vlastnosti, která obsahuje instance modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="5b224-148">Ve scénářích, kde má vlastnosti, které jsou vázány na data z požadavků GET, nastavte `SupportsGet` vlastnost `true`:</span><span class="sxs-lookup"><span data-stu-id="5b224-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="5b224-149">Zdroje</span><span class="sxs-lookup"><span data-stu-id="5b224-149">Sources</span></span>

<span data-ttu-id="5b224-150">Ve výchozím nastavení vazby modelu získá data ve formě dvojic klíč / hodnota z následujících zdrojů v jednom požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="5b224-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="5b224-151">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="5b224-151">Form fields</span></span> 
1. <span data-ttu-id="5b224-152">Text požadavku (pro [řadiči, které mají atribut [objektu ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="5b224-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="5b224-153">Data trasy</span><span class="sxs-lookup"><span data-stu-id="5b224-153">Route data</span></span>
1. <span data-ttu-id="5b224-154">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="5b224-154">Query string parameters</span></span>
1. <span data-ttu-id="5b224-155">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="5b224-155">Uploaded files</span></span> 

<span data-ttu-id="5b224-156">Pro každý cílový parametr nebo vlastnost zdroje jsou prohledávány v uvedeném pořadí v tomto seznamu.</span><span class="sxs-lookup"><span data-stu-id="5b224-156">For each target parameter or property, the sources are scanned in the order indicated in this list.</span></span> <span data-ttu-id="5b224-157">Existuje pár výjimek:</span><span class="sxs-lookup"><span data-stu-id="5b224-157">There are a few exceptions:</span></span>

* <span data-ttu-id="5b224-158">Směrování dat a dotazu řetězcové hodnoty se používají pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="5b224-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="5b224-159">Nahrané soubory jsou propojeny pouze cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="5b224-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="5b224-160">Pokud výchozí chování nedává správných výsledků, můžete použít jednu z následujících atributů k určení zdroje použít pro libovolný daný cíl.</span><span class="sxs-lookup"><span data-stu-id="5b224-160">If the default behavior doesn't give the right results, you can use one of the following attributes to specify the source to use for any given target.</span></span> 

* <span data-ttu-id="5b224-161">[[FromQuery] ](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Získá hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5b224-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="5b224-162">[[FromRoute] ](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Získá hodnoty z dat trasy.</span><span class="sxs-lookup"><span data-stu-id="5b224-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="5b224-163">[[FromForm] ](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Získá hodnoty z polí odeslaného formuláře.</span><span class="sxs-lookup"><span data-stu-id="5b224-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="5b224-164">[[FromBody] ](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Získá hodnoty z textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="5b224-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="5b224-165">[[FromHeader] ](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Získá hodnoty z hlavičky protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b224-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="5b224-166">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="5b224-166">These attributes:</span></span>

* <span data-ttu-id="5b224-167">Jsou přidány do vlastnosti modelu jednotlivě (ne na třídu modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5b224-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="5b224-168">Volitelně můžete přijměte hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5b224-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="5b224-169">Tato možnost je k dispozici v případě, že název vlastnosti neodpovídá hodnotě v identifikátoru požadavku.</span><span class="sxs-lookup"><span data-stu-id="5b224-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="5b224-170">Například může být hodnota v požadavku hlavička s pomlčkou ve svém názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5b224-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="5b224-171">Atribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="5b224-171">[FromBody] attribute</span></span>

<span data-ttu-id="5b224-172">Data těla požadavku je analyzován pomocí vstupní formátování specifické pro typ obsahu požadavku.</span><span class="sxs-lookup"><span data-stu-id="5b224-172">The request body data is parsed by using input formatters specific to the content type of the request.</span></span> <span data-ttu-id="5b224-173">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5b224-173">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="5b224-174">Nemůžete použít `[FromBody]` k více než jeden parametr na metodu akce.</span><span class="sxs-lookup"><span data-stu-id="5b224-174">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="5b224-175">Modul runtime ASP.NET Core deleguje na starost čtení datový proud požadavku na vstupní formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="5b224-175">The ASP.NET Core runtime delegates the responsibility of reading the request stream to the input formatter.</span></span> <span data-ttu-id="5b224-176">Jakmile je datový proud požadavku pro čtení, již není k dispozici ke čtení pro druhé vazby `[FromBody]` parametry.</span><span class="sxs-lookup"><span data-stu-id="5b224-176">Once the request stream is read, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="5b224-177">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5b224-177">Additional sources</span></span>

<span data-ttu-id="5b224-178">Zdroj dat je určena k systému vazby modelu *hodnota poskytovatelé*.</span><span class="sxs-lookup"><span data-stu-id="5b224-178">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="5b224-179">Můžete napsat a registraci zprostředkovatele vlastního hodnot, které získávají data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="5b224-179">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="5b224-180">Například můžete data ze souborů cookie nebo stav relace.</span><span class="sxs-lookup"><span data-stu-id="5b224-180">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="5b224-181">Pokud chcete získat data z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="5b224-181">To get data from a new source:</span></span>

* <span data-ttu-id="5b224-182">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="5b224-182">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="5b224-183">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="5b224-183">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="5b224-184">Registrovat třídu objektů factory v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5b224-184">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="5b224-185">Obsahuje ukázkovou aplikaci [zprostředkovatele hodnot](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) a [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) příklad, který získá hodnoty z soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="5b224-185">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="5b224-186">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5b224-186">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="5b224-187">Kód zobrazený vloží zprostředkovatele vlastní hodnoty koneckonců zprostředkovatele předdefinovaných hodnot.</span><span class="sxs-lookup"><span data-stu-id="5b224-187">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="5b224-188">Aby byla první v seznamu, volání `Insert(0, new CookieValueProviderFactory())` místo `Add`.</span><span class="sxs-lookup"><span data-stu-id="5b224-188">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="5b224-189">Žádný zdroj pro vlastnosti modelu</span><span class="sxs-lookup"><span data-stu-id="5b224-189">No source for a model property</span></span>

<span data-ttu-id="5b224-190">Ve výchozím nastavení se nevytvoří chyby stavu modelu, pokud není nalezena žádná hodnota pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-190">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="5b224-191">Vlastnost je nastavena na hodnotu null nebo výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="5b224-191">The property is set to null or a default value:</span></span>

* <span data-ttu-id="5b224-192">Jednoduché typy s možnou hodnotou Null jsou nastaveny na `null`.</span><span class="sxs-lookup"><span data-stu-id="5b224-192">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="5b224-193">Typy hodnot neumožňující jsou nastaveny na `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="5b224-193">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="5b224-194">Například parametr `int id` je nastavena na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="5b224-194">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="5b224-195">Vazby modelu pro komplexní typy, vytvoří instanci pomocí výchozího konstruktoru, aniž byste museli nastavovat vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5b224-195">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="5b224-196">Pole jsou nastaveny na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastaveny na `null`.</span><span class="sxs-lookup"><span data-stu-id="5b224-196">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="5b224-197">Pokud stav modelu by měl zrušit, když nelze najít v polích formuláře pro vlastnosti modelu, použijte [atribut [BindRequired]](#bindrequired-attribute).</span><span class="sxs-lookup"><span data-stu-id="5b224-197">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="5b224-198">Všimněte si, že tento `[BindRequired]` platí chování vazby modelu z odeslaného formuláře dat, nikoli data XML nebo JSON v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="5b224-198">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="5b224-199">Data těla požadavku se zpracovává souborem [vstup formátovací moduly](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5b224-199">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="5b224-200">Chyby převodu typů</span><span class="sxs-lookup"><span data-stu-id="5b224-200">Type conversion errors</span></span>

<span data-ttu-id="5b224-201">Pokud zdroj nenajde, ale nelze převést na typ cíle, stav modelu označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="5b224-201">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="5b224-202">Cílový parametr nebo vlastnost je nastavena na hodnotu null nebo výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="5b224-202">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="5b224-203">V kontroler API, která má `[ApiController]` atribut, výsledné stavy modelu je neplatný v automatické odpovědi HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="5b224-203">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="5b224-204">Na stránce Razor opětovné zobrazení na stránce s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="5b224-204">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="5b224-205">Ověřování na straně klienta zachycuje většinu chybnými daty, které by jinak odeslána do formuláře pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="5b224-205">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="5b224-206">Toto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="5b224-206">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="5b224-207">Obsahuje ukázkovou aplikaci **odeslat pomocí neplatné datum** tlačítko, které umístí chybnými daty **datum přijetí** pole a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="5b224-207">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="5b224-208">Toto tlačítko ukazuje, jak kód pro opětovné zobrazení na stránce funguje při výskytu chyby převodu dat.</span><span class="sxs-lookup"><span data-stu-id="5b224-208">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="5b224-209">Pokud na stránce se zobrazí znovu v předchozím kódu, neplatný vstup nezobrazuje pole formuláře.</span><span class="sxs-lookup"><span data-stu-id="5b224-209">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="5b224-210">Je to proto, že vlastnost modelu byla nastavena na hodnotu null nebo výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5b224-210">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="5b224-211">Neplatný vstup nezobrazí v chybové zprávě.</span><span class="sxs-lookup"><span data-stu-id="5b224-211">The invalid input does appear in an error message.</span></span> <span data-ttu-id="5b224-212">Pokud ale chcete chybná data v poli formuláře znovu, zvažte provedení vlastnost modelu řetězec a udělat převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="5b224-212">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="5b224-213">Stejné strategie se doporučuje, pokud nechcete, aby chyby převodu typu za následek chyby stavu modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-213">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="5b224-214">V takovém případě zkontrolujte vlastnost modelu řetězec.</span><span class="sxs-lookup"><span data-stu-id="5b224-214">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="5b224-215">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="5b224-215">Simple types</span></span>

<span data-ttu-id="5b224-216">Jednoduché typy, které vazače modelu můžete převést do zdrojové řetězce patří:</span><span class="sxs-lookup"><span data-stu-id="5b224-216">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="5b224-217">Datový typ Boolean</span><span class="sxs-lookup"><span data-stu-id="5b224-217">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="5b224-218">[Bajtů](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="5b224-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="5b224-219">Char</span><span class="sxs-lookup"><span data-stu-id="5b224-219">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="5b224-220">Datum a čas</span><span class="sxs-lookup"><span data-stu-id="5b224-220">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="5b224-221">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="5b224-221">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="5b224-222">Decimal</span><span class="sxs-lookup"><span data-stu-id="5b224-222">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="5b224-223">Double</span><span class="sxs-lookup"><span data-stu-id="5b224-223">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="5b224-224">Výčet</span><span class="sxs-lookup"><span data-stu-id="5b224-224">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="5b224-225">identifikátor GUID</span><span class="sxs-lookup"><span data-stu-id="5b224-225">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="5b224-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="5b224-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="5b224-227">Jeden</span><span class="sxs-lookup"><span data-stu-id="5b224-227">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="5b224-228">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="5b224-228">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="5b224-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="5b224-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="5b224-230">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="5b224-230">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="5b224-231">Verze</span><span class="sxs-lookup"><span data-stu-id="5b224-231">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="5b224-232">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="5b224-232">Complex types</span></span>

<span data-ttu-id="5b224-233">Komplexní typ musí mít veřejný výchozí konstruktor a veřejný zapisovatelný vlastnosti pro vytvoření vazby.</span><span class="sxs-lookup"><span data-stu-id="5b224-233">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="5b224-234">Pokud dojde k vazbě modelu, třída je vytvořen pomocí veřejný výchozí konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5b224-234">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="5b224-235">Pro každou vlastnost komplexní typ vazby modelu vypadá prostřednictvím zdroje pro vzor názvů *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="5b224-235">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="5b224-236">Pokud se nic nenajde, vyhledá pouze *%{Property_Name/* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-236">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="5b224-237">Předpona pro vazbu parametru, je název parametru.</span><span class="sxs-lookup"><span data-stu-id="5b224-237">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="5b224-238">Vazba `PageModel` veřejné vlastnosti, předpona, která je název veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5b224-238">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="5b224-239">Některé atributy mají `Prefix` vlastnost, která vám umožní potlačit výchozí použití názvu parametru nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5b224-239">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="5b224-240">Předpokládejme například, že komplexní typ je následující `Instructor` třídy:</span><span class="sxs-lookup"><span data-stu-id="5b224-240">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="5b224-241">Předpona = název parametru</span><span class="sxs-lookup"><span data-stu-id="5b224-241">Prefix = parameter name</span></span>

<span data-ttu-id="5b224-242">Pokud je model vázán parametr s názvem `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="5b224-242">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="5b224-243">Spuštění vazby modelu projitím zdroje pro klíč `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="5b224-243">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="5b224-244">Pokud, který nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-244">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="5b224-245">Předpona = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="5b224-245">Prefix = property name</span></span>

<span data-ttu-id="5b224-246">Pokud je model vázán vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="5b224-246">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="5b224-247">Spuštění vazby modelu projitím zdroje pro klíč `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="5b224-247">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5b224-248">Pokud, který nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-248">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="5b224-249">Vlastní předponu</span><span class="sxs-lookup"><span data-stu-id="5b224-249">Custom prefix</span></span>

<span data-ttu-id="5b224-250">Pokud je model vázán parametr s názvem `instructorToUpdate` a `Bind` Určuje atribut `Instructor` jako předpona:</span><span class="sxs-lookup"><span data-stu-id="5b224-250">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="5b224-251">Spuštění vazby modelu projitím zdroje pro klíč `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="5b224-251">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="5b224-252">Pokud, který nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="5b224-253">Atributy pro komplexní typ cíle</span><span class="sxs-lookup"><span data-stu-id="5b224-253">Attributes for complex type targets</span></span>

<span data-ttu-id="5b224-254">Několik vestavěné atributy jsou k dispozici pro řízení vazby modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="5b224-254">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="5b224-255">Tyto atributy ovlivňují model vazby při publikování formuláře je dat zdrojové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5b224-255">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="5b224-256">Neovlivňují vstupní formátovací moduly, které proces publikování textem žádosti ve formátu JSON a XML.</span><span class="sxs-lookup"><span data-stu-id="5b224-256">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="5b224-257">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="5b224-257">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="5b224-258">Viz také informace o `[Required]` atribut [ověření modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="5b224-258">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="5b224-259">Atribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="5b224-259">[BindRequired] attribute</span></span>

<span data-ttu-id="5b224-260">Může používat jedině pro vlastnosti modelu, ne na parametry metod.</span><span class="sxs-lookup"><span data-stu-id="5b224-260">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5b224-261">Způsobí, že vazba modelu přidat chyby stavu modelu, pokud vazba nebyla vytvořena pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-261">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="5b224-262">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="5b224-262">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="5b224-263">Atribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="5b224-263">[BindNever] attribute</span></span>

<span data-ttu-id="5b224-264">Může používat jedině pro vlastnosti modelu, ne na parametry metod.</span><span class="sxs-lookup"><span data-stu-id="5b224-264">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="5b224-265">Zabraňuje vazby modelu z nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-265">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="5b224-266">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="5b224-266">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="5b224-267">Atribut [vazby]</span><span class="sxs-lookup"><span data-stu-id="5b224-267">[Bind] attribute</span></span>

<span data-ttu-id="5b224-268">Můžete použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="5b224-268">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="5b224-269">Určuje vlastnosti modelu, které by měl být součástí vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-269">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="5b224-270">V následujícím příkladu, pouze zadané vlastnosti `Instructor` modelu jsou vázána při volání metody všechny obslužné rutiny nebo akce:</span><span class="sxs-lookup"><span data-stu-id="5b224-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="5b224-271">V následujícím příkladu, pouze zadané vlastnosti `Instructor` modelu když jsou vázány `OnPost` volání metody:</span><span class="sxs-lookup"><span data-stu-id="5b224-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="5b224-272">`[Bind]` Atribut lze použít k ochraně proti overposting v *vytvořit* scénáře.</span><span class="sxs-lookup"><span data-stu-id="5b224-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="5b224-273">Nebude fungovat dobře ve scénářích upravit, protože vyloučené vlastnosti nastavena na hodnotu null nebo výchozí hodnotu namísto vlevo beze změny.</span><span class="sxs-lookup"><span data-stu-id="5b224-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="5b224-274">Pro ochranu proti overposting Zobrazit modely doporučujeme místo `[Bind]` atribut.</span><span class="sxs-lookup"><span data-stu-id="5b224-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="5b224-275">Další informace najdete v tématu [Poznámka k zabezpečení o overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="5b224-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="5b224-276">Kolekce</span><span class="sxs-lookup"><span data-stu-id="5b224-276">Collections</span></span>

<span data-ttu-id="5b224-277">Pro cíle, které jsou kolekce typů jednoduché, vazby modelu hledá odpovídající *parameter_name* nebo *%{Property_Name/* .</span><span class="sxs-lookup"><span data-stu-id="5b224-277">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5b224-278">Pokud není nalezena žádná shoda, hledá se pro jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-278">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5b224-279">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5b224-279">For example:</span></span>

* <span data-ttu-id="5b224-280">Předpokládejme, že je parametr vázat pole s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="5b224-280">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="5b224-281">Řetězcová data formuláře nebo dotazu může být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="5b224-281">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="5b224-282">Tento formát je podporován pouze v dat formuláře:</span><span class="sxs-lookup"><span data-stu-id="5b224-282">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="5b224-283">Pro všechny předchozí příklad formátuje vazby modelu předá pole dvou položek `selectedCourses` parametr:</span><span class="sxs-lookup"><span data-stu-id="5b224-283">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5b224-284">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="5b224-284">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="5b224-285">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="5b224-285">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="5b224-286">Formáty dat tohoto dolního indexu čísla použití (...) [0]... [1]...) Ujistěte se, že jsou číslovány postupně na nule.</span><span class="sxs-lookup"><span data-stu-id="5b224-286">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="5b224-287">Pokud jsou všechny mezery v číslování dolního indexu, jsou ignorovány všechny položky po mezery.</span><span class="sxs-lookup"><span data-stu-id="5b224-287">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="5b224-288">Například pokud dolní indexy jsou 0 až 2 místo 0 a 1, druhá položka je ignorována.</span><span class="sxs-lookup"><span data-stu-id="5b224-288">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="5b224-289">slovníky</span><span class="sxs-lookup"><span data-stu-id="5b224-289">Dictionaries</span></span>

<span data-ttu-id="5b224-290">Pro `Dictionary` cíle, vazby modelu hledá odpovídající *parameter_name* nebo *%{Property_Name/* .</span><span class="sxs-lookup"><span data-stu-id="5b224-290">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="5b224-291">Pokud není nalezena žádná shoda, hledá se pro jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="5b224-291">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="5b224-292">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5b224-292">For example:</span></span>

* <span data-ttu-id="5b224-293">Předpokládejme, že je parametr target `Dictionary<string, string>` s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="5b224-293">Suppose the target parameter is a `Dictionary<string, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="5b224-294">Odeslaná data řetězec formuláře nebo dotazu může vypadat například jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="5b224-294">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="5b224-295">Pro všechny předchozí příklad formátuje vazby modelu předá slovník dvě položky `selectedCourses` parametr:</span><span class="sxs-lookup"><span data-stu-id="5b224-295">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="5b224-296">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="5b224-296">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="5b224-297">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="5b224-297">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="5b224-298">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="5b224-298">Special data types</span></span>

<span data-ttu-id="5b224-299">Existují některé speciální datové typy, které dokáže zpracovat vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-299">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="5b224-300">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="5b224-300">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="5b224-301">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="5b224-301">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="5b224-302">Je také podporována `IEnumerable<IFormFile>` u více souborů.</span><span class="sxs-lookup"><span data-stu-id="5b224-302">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="5b224-303">cancellationToken</span><span class="sxs-lookup"><span data-stu-id="5b224-303">CancellationToken</span></span>

<span data-ttu-id="5b224-304">Použít pro zrušení aktivity v asynchronní řadiče.</span><span class="sxs-lookup"><span data-stu-id="5b224-304">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="5b224-305">FormCollection</span><span class="sxs-lookup"><span data-stu-id="5b224-305">FormCollection</span></span>

<span data-ttu-id="5b224-306">Umožňuje načíst všechny hodnoty z odeslaného formuláře data.</span><span class="sxs-lookup"><span data-stu-id="5b224-306">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="5b224-307">Vstupní formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="5b224-307">Input formatters</span></span>

<span data-ttu-id="5b224-308">Data v textu požadavku může být ve formátu JSON, XML nebo jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="5b224-308">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="5b224-309">Tato data analyzovat, model vazby používá *vstupní formátování* , který je nakonfigurovaný pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="5b224-309">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="5b224-310">Ve výchozím nastavení ASP.NET Core zahrnuje formátu JSON vstupní formátovacích modulů pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="5b224-310">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="5b224-311">Můžete přidat další formátování pro jiné typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="5b224-311">You can add other formatters for other content types.</span></span>

<span data-ttu-id="5b224-312">ASP.NET Core vybere vstupní formátovacích modulů na základě [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="5b224-312">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="5b224-313">Pokud je k dispozici žádný atribut, použije [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="5b224-313">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="5b224-314">Použití předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="5b224-314">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="5b224-315">Nainstalujte `Microsoft.AspNetCore.Mvc.Formatters.Xml` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="5b224-315">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="5b224-316">V `Startup.ConfigureServices`, volání <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="5b224-316">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="5b224-317">Použít `Consumes` atribut třídy kontroleru nebo metody akce, které by měli počítat datovou část požadavku XML.</span><span class="sxs-lookup"><span data-stu-id="5b224-317">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="5b224-318">Další informace najdete v tématu [představení serializace XML](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="5b224-318">For more information, see [Introducing XML Serialization](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="5b224-319">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="5b224-319">Exclude specified types from model binding</span></span>

<span data-ttu-id="5b224-320">V modelu a ověření vazby systémech chování vychází [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="5b224-320">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="5b224-321">Můžete přizpůsobit `ModelMetadata` tak, že přidáte podrobnosti zprostředkovatele, aby [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="5b224-321">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="5b224-322">Podrobnosti o předdefinovaných zprostředkovatelé jsou k dispozici pro zakázání ověření pro zadané typy nebo vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-322">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="5b224-323">Chcete-li zakázat vazby modelu zadaného typu všech modelů, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5b224-323">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5b224-324">Například s vazbou modelu zakázat na všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="5b224-324">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="5b224-325">Chcete-li zakázat ověřování na vlastnosti zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5b224-325">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5b224-326">Například chcete-li zakázat ověřování na vlastnosti typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="5b224-326">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="5b224-327">Vazače modelů vlastní</span><span class="sxs-lookup"><span data-stu-id="5b224-327">Custom model binders</span></span>

<span data-ttu-id="5b224-328">Vazby modelu můžete rozšířit pomocí zápisu vlastního modelu pořadače a použití `[ModelBinder]` atribut a vyberte pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="5b224-328">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="5b224-329">Další informace o [vlastní vazba modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="5b224-329">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="5b224-330">Vazby modelu ruční</span><span class="sxs-lookup"><span data-stu-id="5b224-330">Manual model binding</span></span>

<span data-ttu-id="5b224-331">Vazby modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="5b224-331">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="5b224-332">Metoda je definována v obou `ControllerBase` a `PageModel` třídy.</span><span class="sxs-lookup"><span data-stu-id="5b224-332">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="5b224-333">Metoda přetížení umožňují určit zprostředkovatele předponu a hodnota má být použit.</span><span class="sxs-lookup"><span data-stu-id="5b224-333">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="5b224-334">Metoda vrátí `false` selhání vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-334">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="5b224-335">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="5b224-335">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="5b224-336">Atribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="5b224-336">[FromServices] attribute</span></span>

<span data-ttu-id="5b224-337">Název tohoto atributu má následující formát, které určují zdroj dat atributů vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="5b224-337">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="5b224-338">Ale nejedná se o vázání dat z zprostředkovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="5b224-338">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="5b224-339">Získá instanci typu z [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5b224-339">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5b224-340">Jeho účelem je poskytnout alternativu vůči útoku prostřednictvím injektáže konstruktoru pro, když potřebujete službu pouze v případě, že konkrétní metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="5b224-340">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b224-341">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5b224-341">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
