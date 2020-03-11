---
title: Vazba modelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vazba modelu v ASP.NET Core funguje a jak přizpůsobit jeho chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 19580768679f30131683717792252c03aade68f9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666276"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="096e7-103">Vazba modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="096e7-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="096e7-104">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="096e7-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="096e7-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="096e7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="096e7-106">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-106">What is Model binding</span></span>

<span data-ttu-id="096e7-107">Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="096e7-108">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="096e7-109">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="096e7-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="096e7-110">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="096e7-110">Model binding automates this process.</span></span> <span data-ttu-id="096e7-111">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="096e7-111">The model binding system:</span></span>

* <span data-ttu-id="096e7-112">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="096e7-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="096e7-113">Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="096e7-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="096e7-114">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="096e7-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="096e7-115">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="096e7-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="096e7-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="096e7-116">Example</span></span>

<span data-ttu-id="096e7-117">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="096e7-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="096e7-118">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="096e7-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="096e7-119">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="096e7-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="096e7-120">Najde první parametr `GetByID`, celé číslo s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="096e7-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="096e7-121">Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".</span><span class="sxs-lookup"><span data-stu-id="096e7-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="096e7-122">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="096e7-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="096e7-123">Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="096e7-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="096e7-124">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="096e7-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="096e7-125">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="096e7-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="096e7-126">Převede řetězec "true" na Boolean `true`.</span><span class="sxs-lookup"><span data-stu-id="096e7-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="096e7-127">Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="096e7-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="096e7-128">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="096e7-129">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="096e7-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="096e7-130">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="096e7-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="096e7-131">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="096e7-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="096e7-132">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="096e7-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="096e7-133">Cíle</span><span class="sxs-lookup"><span data-stu-id="096e7-133">Targets</span></span>

<span data-ttu-id="096e7-134">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="096e7-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="096e7-135">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="096e7-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="096e7-136">Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="096e7-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="096e7-137">Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="096e7-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="096e7-138">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-138">[BindProperty] attribute</span></span>

<span data-ttu-id="096e7-139">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="096e7-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="096e7-140">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-140">[BindProperties] attribute</span></span>

<span data-ttu-id="096e7-141">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="096e7-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="096e7-142">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="096e7-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="096e7-143">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="096e7-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="096e7-144">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="096e7-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="096e7-145">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="096e7-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="096e7-146">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="096e7-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="096e7-147">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="096e7-148">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:</span><span class="sxs-lookup"><span data-stu-id="096e7-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="096e7-149">Zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-149">Sources</span></span>

<span data-ttu-id="096e7-150">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="096e7-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="096e7-151">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="096e7-151">Form fields</span></span>
1. <span data-ttu-id="096e7-152">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="096e7-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="096e7-153">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="096e7-153">Route data</span></span>
1. <span data-ttu-id="096e7-154">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="096e7-154">Query string parameters</span></span>
1. <span data-ttu-id="096e7-155">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="096e7-155">Uploaded files</span></span>

<span data-ttu-id="096e7-156">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="096e7-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="096e7-157">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="096e7-157">There are a few exceptions:</span></span>

* <span data-ttu-id="096e7-158">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="096e7-159">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="096e7-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="096e7-160">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="096e7-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="096e7-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="096e7-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="096e7-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="096e7-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="096e7-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="096e7-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="096e7-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="096e7-166">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="096e7-166">These attributes:</span></span>

* <span data-ttu-id="096e7-167">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="096e7-168">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="096e7-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="096e7-169">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="096e7-170">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="096e7-171">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-171">[FromBody] attribute</span></span>

<span data-ttu-id="096e7-172">Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="096e7-173">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="096e7-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="096e7-174">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="096e7-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="096e7-175">Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují.</span><span class="sxs-lookup"><span data-stu-id="096e7-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="096e7-176">Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="096e7-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="096e7-177">Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="096e7-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="096e7-178">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-178">In the preceding example:</span></span>

* <span data-ttu-id="096e7-179">Atribut `[FromQuery]` je ignorován.</span><span class="sxs-lookup"><span data-stu-id="096e7-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="096e7-180">Vlastnost `Breed` není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="096e7-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="096e7-181">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="096e7-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="096e7-182">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.</span><span class="sxs-lookup"><span data-stu-id="096e7-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="096e7-183">Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="096e7-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="096e7-184">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="096e7-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="096e7-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-185">Additional sources</span></span>

<span data-ttu-id="096e7-186">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="096e7-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="096e7-187">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="096e7-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="096e7-188">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="096e7-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="096e7-189">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="096e7-189">To get data from a new source:</span></span>

* <span data-ttu-id="096e7-190">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="096e7-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="096e7-191">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="096e7-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="096e7-192">Zaregistrujte třídu factory v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="096e7-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="096e7-193">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="096e7-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="096e7-194">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="096e7-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="096e7-195">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="096e7-196">Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.</span><span class="sxs-lookup"><span data-stu-id="096e7-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="096e7-197">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-197">No source for a model property</span></span>

<span data-ttu-id="096e7-198">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="096e7-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="096e7-199">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="096e7-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="096e7-200">Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.</span><span class="sxs-lookup"><span data-stu-id="096e7-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="096e7-201">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="096e7-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="096e7-202">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="096e7-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="096e7-203">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="096e7-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="096e7-204">Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.</span><span class="sxs-lookup"><span data-stu-id="096e7-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="096e7-205">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="096e7-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="096e7-206">Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="096e7-207">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="096e7-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="096e7-208">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="096e7-208">Type conversion errors</span></span>

<span data-ttu-id="096e7-209">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="096e7-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="096e7-210">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="096e7-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="096e7-211">V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="096e7-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="096e7-212">Na stránce Razor znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="096e7-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="096e7-213">Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="096e7-214">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="096e7-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="096e7-215">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="096e7-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="096e7-216">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="096e7-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="096e7-217">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="096e7-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="096e7-218">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="096e7-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="096e7-219">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="096e7-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="096e7-220">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="096e7-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="096e7-221">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="096e7-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="096e7-222">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="096e7-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="096e7-223">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="096e7-223">Simple types</span></span>

<span data-ttu-id="096e7-224">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="096e7-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="096e7-225">Datového</span><span class="sxs-lookup"><span data-stu-id="096e7-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="096e7-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="096e7-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="096e7-227">Char</span><span class="sxs-lookup"><span data-stu-id="096e7-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="096e7-228">Hodnotu</span><span class="sxs-lookup"><span data-stu-id="096e7-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="096e7-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="096e7-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="096e7-230">Notaci</span><span class="sxs-lookup"><span data-stu-id="096e7-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="096e7-231">Klepat</span><span class="sxs-lookup"><span data-stu-id="096e7-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="096e7-232">Vytváření</span><span class="sxs-lookup"><span data-stu-id="096e7-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="096e7-233">Hlavních</span><span class="sxs-lookup"><span data-stu-id="096e7-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="096e7-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="096e7-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="096e7-235">Konkrétní</span><span class="sxs-lookup"><span data-stu-id="096e7-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="096e7-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="096e7-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="096e7-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="096e7-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="096e7-238">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="096e7-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="096e7-239">Verze</span><span class="sxs-lookup"><span data-stu-id="096e7-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="096e7-240">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="096e7-240">Complex types</span></span>

<span data-ttu-id="096e7-241">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="096e7-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="096e7-242">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="096e7-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="096e7-243">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="096e7-244">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="096e7-245">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="096e7-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="096e7-246">Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="096e7-247">Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="096e7-248">Předpokládejme například, že komplexní typ je následující třída `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="096e7-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="096e7-249">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="096e7-249">Prefix = parameter name</span></span>

<span data-ttu-id="096e7-250">Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="096e7-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="096e7-251">Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="096e7-252">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="096e7-253">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="096e7-253">Prefix = property name</span></span>

<span data-ttu-id="096e7-254">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="096e7-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="096e7-255">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="096e7-256">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="096e7-257">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="096e7-257">Custom prefix</span></span>

<span data-ttu-id="096e7-258">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="096e7-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="096e7-259">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="096e7-260">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="096e7-261">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="096e7-261">Attributes for complex type targets</span></span>

<span data-ttu-id="096e7-262">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="096e7-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="096e7-263">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="096e7-264">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="096e7-265">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="096e7-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="096e7-266">Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="096e7-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="096e7-267">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-267">[BindRequired] attribute</span></span>

<span data-ttu-id="096e7-268">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="096e7-269">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="096e7-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="096e7-270">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="096e7-271">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-271">[BindNever] attribute</span></span>

<span data-ttu-id="096e7-272">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="096e7-273">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="096e7-274">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="096e7-275">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-275">[Bind] attribute</span></span>

<span data-ttu-id="096e7-276">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="096e7-277">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="096e7-278">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:</span><span class="sxs-lookup"><span data-stu-id="096e7-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="096e7-279">V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:</span><span class="sxs-lookup"><span data-stu-id="096e7-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="096e7-280">Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="096e7-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="096e7-281">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="096e7-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="096e7-282">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="096e7-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="096e7-283">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="096e7-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="096e7-284">Kolekce</span><span class="sxs-lookup"><span data-stu-id="096e7-284">Collections</span></span>

<span data-ttu-id="096e7-285">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="096e7-286">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="096e7-287">Příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-287">For example:</span></span>

* <span data-ttu-id="096e7-288">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="096e7-289">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="096e7-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="096e7-290">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="096e7-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="096e7-291">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="096e7-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="096e7-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="096e7-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="096e7-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="096e7-294">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="096e7-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="096e7-295">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="096e7-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="096e7-296">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="096e7-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="096e7-297">Slovníky</span><span class="sxs-lookup"><span data-stu-id="096e7-297">Dictionaries</span></span>

<span data-ttu-id="096e7-298">U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="096e7-299">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="096e7-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-300">For example:</span></span>

* <span data-ttu-id="096e7-301">Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="096e7-302">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="096e7-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="096e7-303">Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="096e7-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="096e7-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="096e7-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="096e7-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="096e7-306">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="096e7-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="096e7-307">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="096e7-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="096e7-308">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="096e7-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="096e7-309">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="096e7-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="096e7-310">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="096e7-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="096e7-311">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="096e7-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="096e7-312">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="096e7-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="096e7-313">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="096e7-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="096e7-314">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="096e7-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="096e7-315">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="096e7-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="096e7-316">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="096e7-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="096e7-317">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="096e7-317">Special data types</span></span>

<span data-ttu-id="096e7-318">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="096e7-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="096e7-319">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="096e7-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="096e7-320">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="096e7-321">Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.</span><span class="sxs-lookup"><span data-stu-id="096e7-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="096e7-322">cancellationToken</span><span class="sxs-lookup"><span data-stu-id="096e7-322">CancellationToken</span></span>

<span data-ttu-id="096e7-323">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="096e7-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="096e7-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="096e7-324">FormCollection</span></span>

<span data-ttu-id="096e7-325">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="096e7-326">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="096e7-326">Input formatters</span></span>

<span data-ttu-id="096e7-327">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="096e7-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="096e7-328">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="096e7-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="096e7-329">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="096e7-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="096e7-330">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="096e7-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="096e7-331">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="096e7-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="096e7-332">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="096e7-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="096e7-333">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="096e7-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="096e7-334">Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="096e7-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="096e7-335">V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="096e7-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="096e7-336">Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="096e7-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="096e7-337">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="096e7-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="096e7-338">Přizpůsobení vazby modelu pomocí vstupních formátovacích prvků</span><span class="sxs-lookup"><span data-stu-id="096e7-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="096e7-339">Vstupní formátovací modul má plnou zodpovědnost za čtení dat z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="096e7-340">Chcete-li tento proces přizpůsobit, nakonfigurujte rozhraní API používaná vstupním formátovacím modulem.</span><span class="sxs-lookup"><span data-stu-id="096e7-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="096e7-341">Tato část popisuje, jak upravit vstupní formátovací modul založený na `System.Text.Json`pro pochopení vlastního typu s názvem `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="096e7-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="096e7-342">Vezměte v úvahu následující model, který obsahuje vlastní vlastnost `ObjectId` s názvem `Id`:</span><span class="sxs-lookup"><span data-stu-id="096e7-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="096e7-343">Chcete-li přizpůsobit proces vazby modelu při použití `System.Text.Json`, vytvořte třídu odvozenou z <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="096e7-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="096e7-344">Chcete-li použít vlastní převaděč, použijte atribut <xref:System.Text.Json.Serialization.JsonConverterAttribute> pro typ.</span><span class="sxs-lookup"><span data-stu-id="096e7-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="096e7-345">V následujícím příkladu je typ `ObjectId` nakonfigurovaný pomocí `ObjectIdConverter` jako svůj vlastní převaděč:</span><span class="sxs-lookup"><span data-stu-id="096e7-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="096e7-346">Další informace najdete v tématu [jak psát vlastní převaděče](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="096e7-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="096e7-347">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="096e7-348">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="096e7-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="096e7-349">`ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="096e7-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="096e7-350">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="096e7-351">Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="096e7-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="096e7-352">Například pro zákaz vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="096e7-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="096e7-353">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="096e7-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="096e7-354">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="096e7-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="096e7-355">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="096e7-355">Custom model binders</span></span>

<span data-ttu-id="096e7-356">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="096e7-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="096e7-357">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="096e7-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="096e7-358">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-358">Manual model binding</span></span> 

<span data-ttu-id="096e7-359">Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="096e7-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="096e7-360">Metoda je definována v obou třídách `ControllerBase` i `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="096e7-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="096e7-361">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="096e7-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="096e7-362">Metoda vrátí `false`, pokud se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="096e7-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="096e7-363">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="096e7-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> používá zprostředkovatele hodnot k získávání dat z textu formuláře, řetězce dotazu a dat směrování.</span><span class="sxs-lookup"><span data-stu-id="096e7-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="096e7-365">`TryUpdateModelAsync` je obvykle:</span><span class="sxs-lookup"><span data-stu-id="096e7-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="096e7-366">Používá se s aplikacemi Razor Pages a MVC k tomu, aby se zabránilo převzetí služeb při selhání pomocí řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="096e7-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="096e7-367">Nepoužívá se s webovým rozhraním API, pokud se nevyužívá pro data formulářů, řetězce dotazů a data směrování.</span><span class="sxs-lookup"><span data-stu-id="096e7-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="096e7-368">Koncové body webového rozhraní API, které využívají [Formátovací moduly vstupu](#input-formatters) JSON k deserializaci těla požadavku do objektu.</span><span class="sxs-lookup"><span data-stu-id="096e7-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="096e7-369">Další informace najdete v tématu [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="096e7-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="096e7-370">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-370">[FromServices] attribute</span></span>

<span data-ttu-id="096e7-371">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="096e7-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="096e7-372">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="096e7-373">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="096e7-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="096e7-374">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="096e7-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="096e7-375">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="096e7-376">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="096e7-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="096e7-377">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="096e7-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="096e7-378">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-378">What is Model binding</span></span>

<span data-ttu-id="096e7-379">Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="096e7-380">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="096e7-381">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="096e7-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="096e7-382">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="096e7-382">Model binding automates this process.</span></span> <span data-ttu-id="096e7-383">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="096e7-383">The model binding system:</span></span>

* <span data-ttu-id="096e7-384">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="096e7-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="096e7-385">Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="096e7-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="096e7-386">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="096e7-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="096e7-387">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="096e7-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="096e7-388">Příklad</span><span class="sxs-lookup"><span data-stu-id="096e7-388">Example</span></span>

<span data-ttu-id="096e7-389">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="096e7-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="096e7-390">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="096e7-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="096e7-391">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="096e7-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="096e7-392">Najde první parametr `GetByID`, celé číslo s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="096e7-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="096e7-393">Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".</span><span class="sxs-lookup"><span data-stu-id="096e7-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="096e7-394">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="096e7-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="096e7-395">Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="096e7-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="096e7-396">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="096e7-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="096e7-397">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="096e7-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="096e7-398">Převede řetězec "true" na Boolean `true`.</span><span class="sxs-lookup"><span data-stu-id="096e7-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="096e7-399">Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="096e7-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="096e7-400">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="096e7-401">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="096e7-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="096e7-402">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="096e7-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="096e7-403">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="096e7-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="096e7-404">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="096e7-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="096e7-405">Cíle</span><span class="sxs-lookup"><span data-stu-id="096e7-405">Targets</span></span>

<span data-ttu-id="096e7-406">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="096e7-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="096e7-407">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="096e7-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="096e7-408">Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="096e7-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="096e7-409">Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="096e7-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="096e7-410">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-410">[BindProperty] attribute</span></span>

<span data-ttu-id="096e7-411">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="096e7-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="096e7-412">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-412">[BindProperties] attribute</span></span>

<span data-ttu-id="096e7-413">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="096e7-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="096e7-414">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="096e7-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="096e7-415">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="096e7-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="096e7-416">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="096e7-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="096e7-417">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="096e7-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="096e7-418">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="096e7-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="096e7-419">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="096e7-420">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:</span><span class="sxs-lookup"><span data-stu-id="096e7-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="096e7-421">Zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-421">Sources</span></span>

<span data-ttu-id="096e7-422">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="096e7-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="096e7-423">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="096e7-423">Form fields</span></span>
1. <span data-ttu-id="096e7-424">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="096e7-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="096e7-425">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="096e7-425">Route data</span></span>
1. <span data-ttu-id="096e7-426">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="096e7-426">Query string parameters</span></span>
1. <span data-ttu-id="096e7-427">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="096e7-427">Uploaded files</span></span>

<span data-ttu-id="096e7-428">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="096e7-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="096e7-429">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="096e7-429">There are a few exceptions:</span></span>

* <span data-ttu-id="096e7-430">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="096e7-431">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="096e7-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="096e7-432">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="096e7-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="096e7-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="096e7-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="096e7-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="096e7-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="096e7-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="096e7-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="096e7-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="096e7-438">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="096e7-438">These attributes:</span></span>

* <span data-ttu-id="096e7-439">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="096e7-440">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="096e7-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="096e7-441">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="096e7-442">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="096e7-443">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-443">[FromBody] attribute</span></span>

<span data-ttu-id="096e7-444">Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="096e7-445">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="096e7-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="096e7-446">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="096e7-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="096e7-447">Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují.</span><span class="sxs-lookup"><span data-stu-id="096e7-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="096e7-448">Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="096e7-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="096e7-449">Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="096e7-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="096e7-450">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="096e7-450">In the preceding example:</span></span>

* <span data-ttu-id="096e7-451">Atribut `[FromQuery]` je ignorován.</span><span class="sxs-lookup"><span data-stu-id="096e7-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="096e7-452">Vlastnost `Breed` není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="096e7-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="096e7-453">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="096e7-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="096e7-454">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.</span><span class="sxs-lookup"><span data-stu-id="096e7-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="096e7-455">Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="096e7-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="096e7-456">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="096e7-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="096e7-457">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-457">Additional sources</span></span>

<span data-ttu-id="096e7-458">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="096e7-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="096e7-459">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="096e7-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="096e7-460">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="096e7-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="096e7-461">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="096e7-461">To get data from a new source:</span></span>

* <span data-ttu-id="096e7-462">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="096e7-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="096e7-463">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="096e7-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="096e7-464">Zaregistrujte třídu factory v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="096e7-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="096e7-465">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="096e7-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="096e7-466">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="096e7-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="096e7-467">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="096e7-468">Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.</span><span class="sxs-lookup"><span data-stu-id="096e7-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="096e7-469">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-469">No source for a model property</span></span>

<span data-ttu-id="096e7-470">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="096e7-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="096e7-471">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="096e7-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="096e7-472">Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.</span><span class="sxs-lookup"><span data-stu-id="096e7-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="096e7-473">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="096e7-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="096e7-474">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="096e7-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="096e7-475">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="096e7-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="096e7-476">Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.</span><span class="sxs-lookup"><span data-stu-id="096e7-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="096e7-477">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="096e7-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="096e7-478">Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="096e7-479">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="096e7-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="096e7-480">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="096e7-480">Type conversion errors</span></span>

<span data-ttu-id="096e7-481">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="096e7-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="096e7-482">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="096e7-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="096e7-483">V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="096e7-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="096e7-484">Na stránce Razor znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="096e7-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="096e7-485">Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="096e7-486">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="096e7-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="096e7-487">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="096e7-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="096e7-488">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="096e7-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="096e7-489">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="096e7-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="096e7-490">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="096e7-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="096e7-491">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="096e7-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="096e7-492">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="096e7-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="096e7-493">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="096e7-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="096e7-494">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="096e7-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="096e7-495">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="096e7-495">Simple types</span></span>

<span data-ttu-id="096e7-496">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="096e7-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="096e7-497">Datového</span><span class="sxs-lookup"><span data-stu-id="096e7-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="096e7-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="096e7-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="096e7-499">Char</span><span class="sxs-lookup"><span data-stu-id="096e7-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="096e7-500">Hodnotu</span><span class="sxs-lookup"><span data-stu-id="096e7-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="096e7-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="096e7-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="096e7-502">Notaci</span><span class="sxs-lookup"><span data-stu-id="096e7-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="096e7-503">Klepat</span><span class="sxs-lookup"><span data-stu-id="096e7-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="096e7-504">Vytváření</span><span class="sxs-lookup"><span data-stu-id="096e7-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="096e7-505">Hlavních</span><span class="sxs-lookup"><span data-stu-id="096e7-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="096e7-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="096e7-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="096e7-507">Konkrétní</span><span class="sxs-lookup"><span data-stu-id="096e7-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="096e7-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="096e7-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="096e7-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="096e7-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="096e7-510">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="096e7-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="096e7-511">Verze</span><span class="sxs-lookup"><span data-stu-id="096e7-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="096e7-512">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="096e7-512">Complex types</span></span>

<span data-ttu-id="096e7-513">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="096e7-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="096e7-514">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="096e7-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="096e7-515">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="096e7-516">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="096e7-517">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="096e7-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="096e7-518">Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="096e7-519">Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="096e7-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="096e7-520">Předpokládejme například, že komplexní typ je následující třída `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="096e7-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="096e7-521">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="096e7-521">Prefix = parameter name</span></span>

<span data-ttu-id="096e7-522">Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="096e7-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="096e7-523">Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="096e7-524">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="096e7-525">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="096e7-525">Prefix = property name</span></span>

<span data-ttu-id="096e7-526">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="096e7-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="096e7-527">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="096e7-528">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="096e7-529">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="096e7-529">Custom prefix</span></span>

<span data-ttu-id="096e7-530">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="096e7-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="096e7-531">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="096e7-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="096e7-532">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="096e7-533">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="096e7-533">Attributes for complex type targets</span></span>

<span data-ttu-id="096e7-534">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="096e7-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="096e7-535">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="096e7-536">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="096e7-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="096e7-537">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="096e7-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="096e7-538">Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="096e7-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="096e7-539">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-539">[BindRequired] attribute</span></span>

<span data-ttu-id="096e7-540">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="096e7-541">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="096e7-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="096e7-542">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="096e7-543">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-543">[BindNever] attribute</span></span>

<span data-ttu-id="096e7-544">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="096e7-545">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="096e7-546">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="096e7-547">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-547">[Bind] attribute</span></span>

<span data-ttu-id="096e7-548">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="096e7-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="096e7-549">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="096e7-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="096e7-550">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:</span><span class="sxs-lookup"><span data-stu-id="096e7-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="096e7-551">V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:</span><span class="sxs-lookup"><span data-stu-id="096e7-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="096e7-552">Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="096e7-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="096e7-553">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="096e7-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="096e7-554">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="096e7-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="096e7-555">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="096e7-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="096e7-556">Kolekce</span><span class="sxs-lookup"><span data-stu-id="096e7-556">Collections</span></span>

<span data-ttu-id="096e7-557">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="096e7-558">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="096e7-559">Příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-559">For example:</span></span>

* <span data-ttu-id="096e7-560">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="096e7-561">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="096e7-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="096e7-562">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="096e7-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="096e7-563">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="096e7-564">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="096e7-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="096e7-565">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="096e7-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="096e7-566">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="096e7-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="096e7-567">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="096e7-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="096e7-568">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="096e7-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="096e7-569">Slovníky</span><span class="sxs-lookup"><span data-stu-id="096e7-569">Dictionaries</span></span>

<span data-ttu-id="096e7-570">U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="096e7-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="096e7-571">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="096e7-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="096e7-572">Příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-572">For example:</span></span>

* <span data-ttu-id="096e7-573">Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="096e7-574">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="096e7-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="096e7-575">Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="096e7-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="096e7-576">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="096e7-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="096e7-577">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="096e7-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="096e7-578">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="096e7-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="096e7-579">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="096e7-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="096e7-580">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="096e7-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="096e7-581">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="096e7-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="096e7-582">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="096e7-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="096e7-583">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="096e7-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="096e7-584">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="096e7-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="096e7-585">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="096e7-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="096e7-586">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="096e7-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="096e7-587">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="096e7-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="096e7-588">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="096e7-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="096e7-589">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="096e7-589">Special data types</span></span>

<span data-ttu-id="096e7-590">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="096e7-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="096e7-591">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="096e7-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="096e7-592">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="096e7-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="096e7-593">Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.</span><span class="sxs-lookup"><span data-stu-id="096e7-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="096e7-594">cancellationToken</span><span class="sxs-lookup"><span data-stu-id="096e7-594">CancellationToken</span></span>

<span data-ttu-id="096e7-595">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="096e7-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="096e7-596">FormCollection</span><span class="sxs-lookup"><span data-stu-id="096e7-596">FormCollection</span></span>

<span data-ttu-id="096e7-597">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="096e7-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="096e7-598">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="096e7-598">Input formatters</span></span>

<span data-ttu-id="096e7-599">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="096e7-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="096e7-600">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="096e7-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="096e7-601">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="096e7-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="096e7-602">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="096e7-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="096e7-603">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="096e7-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="096e7-604">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="096e7-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="096e7-605">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="096e7-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="096e7-606">Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="096e7-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="096e7-607">V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="096e7-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="096e7-608">Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="096e7-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="096e7-609">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="096e7-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="096e7-610">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="096e7-611">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="096e7-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="096e7-612">`ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="096e7-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="096e7-613">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="096e7-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="096e7-614">Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="096e7-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="096e7-615">Například pro zákaz vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="096e7-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="096e7-616">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="096e7-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="096e7-617">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="096e7-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="096e7-618">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="096e7-618">Custom model binders</span></span>

<span data-ttu-id="096e7-619">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="096e7-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="096e7-620">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="096e7-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="096e7-621">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="096e7-621">Manual model binding</span></span>

<span data-ttu-id="096e7-622">Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="096e7-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="096e7-623">Metoda je definována v obou třídách `ControllerBase` i `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="096e7-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="096e7-624">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="096e7-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="096e7-625">Metoda vrátí `false`, pokud se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="096e7-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="096e7-626">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="096e7-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="096e7-627">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="096e7-627">[FromServices] attribute</span></span>

<span data-ttu-id="096e7-628">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="096e7-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="096e7-629">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="096e7-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="096e7-630">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="096e7-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="096e7-631">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="096e7-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="096e7-632">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="096e7-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
