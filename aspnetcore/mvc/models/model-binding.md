---
title: Vazba modelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vazba modelu v ASP.NET Core funguje a jak přizpůsobit jeho chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: da6cc25e0bbb1b2301529b34eab4c91f9ccb46eb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944292"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="f022a-103">Vazba modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f022a-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f022a-104">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="f022a-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="f022a-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f022a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="f022a-106">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-106">What is Model binding</span></span>

<span data-ttu-id="f022a-107">Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="f022a-108">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="f022a-109">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="f022a-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="f022a-110">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="f022a-110">Model binding automates this process.</span></span> <span data-ttu-id="f022a-111">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="f022a-111">The model binding system:</span></span>

* <span data-ttu-id="f022a-112">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="f022a-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="f022a-113">Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="f022a-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="f022a-114">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="f022a-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="f022a-115">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="f022a-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="f022a-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="f022a-116">Example</span></span>

<span data-ttu-id="f022a-117">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f022a-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="f022a-118">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="f022a-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="f022a-119">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f022a-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="f022a-120">Najde první parametr `GetByID`, celé číslo s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="f022a-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="f022a-121">Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".</span><span class="sxs-lookup"><span data-stu-id="f022a-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="f022a-122">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="f022a-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="f022a-123">Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f022a-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="f022a-124">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="f022a-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="f022a-125">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f022a-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="f022a-126">Převede řetězec "true" na Boolean `true`.</span><span class="sxs-lookup"><span data-stu-id="f022a-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="f022a-127">Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f022a-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="f022a-128">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="f022a-129">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="f022a-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="f022a-130">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f022a-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="f022a-131">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="f022a-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="f022a-132">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="f022a-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="f022a-133">Cíle</span><span class="sxs-lookup"><span data-stu-id="f022a-133">Targets</span></span>

<span data-ttu-id="f022a-134">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="f022a-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="f022a-135">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="f022a-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="f022a-136">Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="f022a-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="f022a-137">Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="f022a-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="f022a-138">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-138">[BindProperty] attribute</span></span>

<span data-ttu-id="f022a-139">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f022a-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="f022a-140">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-140">[BindProperties] attribute</span></span>

<span data-ttu-id="f022a-141">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="f022a-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="f022a-142">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="f022a-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="f022a-143">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="f022a-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="f022a-144">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="f022a-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="f022a-145">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="f022a-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="f022a-146">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="f022a-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="f022a-147">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="f022a-148">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:</span><span class="sxs-lookup"><span data-stu-id="f022a-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="f022a-149">Zdroje</span><span class="sxs-lookup"><span data-stu-id="f022a-149">Sources</span></span>

<span data-ttu-id="f022a-150">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="f022a-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="f022a-151">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="f022a-151">Form fields</span></span>
1. <span data-ttu-id="f022a-152">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="f022a-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="f022a-153">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="f022a-153">Route data</span></span>
1. <span data-ttu-id="f022a-154">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="f022a-154">Query string parameters</span></span>
1. <span data-ttu-id="f022a-155">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="f022a-155">Uploaded files</span></span>

<span data-ttu-id="f022a-156">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="f022a-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="f022a-157">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="f022a-157">There are a few exceptions:</span></span>

* <span data-ttu-id="f022a-158">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="f022a-159">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="f022a-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="f022a-160">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="f022a-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="f022a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f022a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="f022a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="f022a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="f022a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="f022a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="f022a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="f022a-166">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="f022a-166">These attributes:</span></span>

* <span data-ttu-id="f022a-167">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="f022a-168">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f022a-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="f022a-169">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="f022a-170">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="f022a-171">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-171">[FromBody] attribute</span></span>

<span data-ttu-id="f022a-172">Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="f022a-173">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="f022a-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="f022a-174">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f022a-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="f022a-175">Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují.</span><span class="sxs-lookup"><span data-stu-id="f022a-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="f022a-176">Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="f022a-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="f022a-177">Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="f022a-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="f022a-178">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-178">In the preceding example:</span></span>

* <span data-ttu-id="f022a-179">Atribut `[FromQuery]` je ignorován.</span><span class="sxs-lookup"><span data-stu-id="f022a-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="f022a-180">Vlastnost `Breed` není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f022a-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="f022a-181">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="f022a-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="f022a-182">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.</span><span class="sxs-lookup"><span data-stu-id="f022a-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="f022a-183">Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="f022a-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="f022a-184">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="f022a-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="f022a-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f022a-185">Additional sources</span></span>

<span data-ttu-id="f022a-186">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="f022a-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="f022a-187">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="f022a-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="f022a-188">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="f022a-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="f022a-189">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="f022a-189">To get data from a new source:</span></span>

* <span data-ttu-id="f022a-190">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="f022a-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="f022a-191">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="f022a-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="f022a-192">Zaregistrujte třídu factory v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f022a-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="f022a-193">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f022a-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="f022a-194">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f022a-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="f022a-195">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="f022a-196">Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.</span><span class="sxs-lookup"><span data-stu-id="f022a-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="f022a-197">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-197">No source for a model property</span></span>

<span data-ttu-id="f022a-198">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="f022a-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="f022a-199">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f022a-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="f022a-200">Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.</span><span class="sxs-lookup"><span data-stu-id="f022a-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="f022a-201">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="f022a-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="f022a-202">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="f022a-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="f022a-203">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="f022a-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="f022a-204">Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.</span><span class="sxs-lookup"><span data-stu-id="f022a-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="f022a-205">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f022a-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="f022a-206">Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="f022a-207">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="f022a-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="f022a-208">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="f022a-208">Type conversion errors</span></span>

<span data-ttu-id="f022a-209">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="f022a-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="f022a-210">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f022a-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="f022a-211">V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="f022a-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="f022a-212">Na stránce Razor znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="f022a-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="f022a-213">Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="f022a-214">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="f022a-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="f022a-215">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="f022a-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="f022a-216">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="f022a-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="f022a-217">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f022a-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="f022a-218">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f022a-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="f022a-219">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f022a-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="f022a-220">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="f022a-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="f022a-221">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="f022a-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="f022a-222">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="f022a-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="f022a-223">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="f022a-223">Simple types</span></span>

<span data-ttu-id="f022a-224">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="f022a-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="f022a-225">Datový typ Boolean</span><span class="sxs-lookup"><span data-stu-id="f022a-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="f022a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="f022a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="f022a-227">Char</span><span class="sxs-lookup"><span data-stu-id="f022a-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="f022a-228">Hodnotu</span><span class="sxs-lookup"><span data-stu-id="f022a-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="f022a-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="f022a-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="f022a-230">Notaci</span><span class="sxs-lookup"><span data-stu-id="f022a-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="f022a-231">Klepat</span><span class="sxs-lookup"><span data-stu-id="f022a-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="f022a-232">Vytváření</span><span class="sxs-lookup"><span data-stu-id="f022a-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="f022a-233">Hlavních</span><span class="sxs-lookup"><span data-stu-id="f022a-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="f022a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="f022a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="f022a-235">Konkrétní</span><span class="sxs-lookup"><span data-stu-id="f022a-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="f022a-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="f022a-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="f022a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="f022a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="f022a-238">Uri</span><span class="sxs-lookup"><span data-stu-id="f022a-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="f022a-239">Verze</span><span class="sxs-lookup"><span data-stu-id="f022a-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="f022a-240">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="f022a-240">Complex types</span></span>

<span data-ttu-id="f022a-241">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="f022a-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="f022a-242">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f022a-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="f022a-243">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="f022a-244">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="f022a-245">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="f022a-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="f022a-246">Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="f022a-247">Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="f022a-248">Předpokládejme například, že komplexní typ je následující třída `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="f022a-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="f022a-249">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="f022a-249">Prefix = parameter name</span></span>

<span data-ttu-id="f022a-250">Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="f022a-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="f022a-251">Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="f022a-252">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="f022a-253">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="f022a-253">Prefix = property name</span></span>

<span data-ttu-id="f022a-254">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="f022a-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="f022a-255">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f022a-256">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="f022a-257">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="f022a-257">Custom prefix</span></span>

<span data-ttu-id="f022a-258">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="f022a-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="f022a-259">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f022a-260">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="f022a-261">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="f022a-261">Attributes for complex type targets</span></span>

<span data-ttu-id="f022a-262">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="f022a-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="f022a-263">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="f022a-264">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="f022a-265">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f022a-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="f022a-266">Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="f022a-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="f022a-267">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-267">[BindRequired] attribute</span></span>

<span data-ttu-id="f022a-268">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f022a-269">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="f022a-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="f022a-270">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="f022a-271">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-271">[BindNever] attribute</span></span>

<span data-ttu-id="f022a-272">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f022a-273">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="f022a-274">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="f022a-275">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-275">[Bind] attribute</span></span>

<span data-ttu-id="f022a-276">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="f022a-277">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="f022a-278">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:</span><span class="sxs-lookup"><span data-stu-id="f022a-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="f022a-279">V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:</span><span class="sxs-lookup"><span data-stu-id="f022a-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="f022a-280">Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="f022a-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="f022a-281">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="f022a-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="f022a-282">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="f022a-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="f022a-283">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="f022a-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="f022a-284">Kolekce</span><span class="sxs-lookup"><span data-stu-id="f022a-284">Collections</span></span>

<span data-ttu-id="f022a-285">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f022a-286">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f022a-287">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-287">For example:</span></span>

* <span data-ttu-id="f022a-288">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="f022a-289">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="f022a-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="f022a-290">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="f022a-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="f022a-291">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f022a-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="f022a-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="f022a-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="f022a-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="f022a-294">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="f022a-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="f022a-295">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="f022a-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="f022a-296">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="f022a-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="f022a-297">Slovníky</span><span class="sxs-lookup"><span data-stu-id="f022a-297">Dictionaries</span></span>

<span data-ttu-id="f022a-298">U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f022a-299">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f022a-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-300">For example:</span></span>

* <span data-ttu-id="f022a-301">Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="f022a-302">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="f022a-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="f022a-303">Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f022a-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="f022a-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="f022a-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="f022a-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="f022a-306">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="f022a-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="f022a-307">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="f022a-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="f022a-308">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="f022a-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="f022a-309">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="f022a-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="f022a-310">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="f022a-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="f022a-311">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="f022a-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="f022a-312">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="f022a-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="f022a-313">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="f022a-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="f022a-314">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="f022a-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="f022a-315">Nahraďte [hodnotu jazykové verze](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="f022a-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="f022a-316">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="f022a-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="f022a-317">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="f022a-317">Special data types</span></span>

<span data-ttu-id="f022a-318">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="f022a-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="f022a-319">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="f022a-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="f022a-320">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="f022a-321">Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.</span><span class="sxs-lookup"><span data-stu-id="f022a-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="f022a-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="f022a-322">CancellationToken</span></span>

<span data-ttu-id="f022a-323">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="f022a-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="f022a-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="f022a-324">FormCollection</span></span>

<span data-ttu-id="f022a-325">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="f022a-326">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="f022a-326">Input formatters</span></span>

<span data-ttu-id="f022a-327">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="f022a-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="f022a-328">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="f022a-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="f022a-329">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="f022a-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="f022a-330">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="f022a-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="f022a-331">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f022a-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="f022a-332">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="f022a-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="f022a-333">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="f022a-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="f022a-334">Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="f022a-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="f022a-335">V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="f022a-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="f022a-336">Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="f022a-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="f022a-337">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="f022a-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="f022a-338">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-338">Exclude specified types from model binding</span></span>

<span data-ttu-id="f022a-339">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="f022a-339">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="f022a-340">`ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="f022a-340">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="f022a-341">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-341">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="f022a-342">Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="f022a-342">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f022a-343">Například pro zákaz vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="f022a-343">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="f022a-344">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="f022a-344">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f022a-345">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="f022a-345">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="f022a-346">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="f022a-346">Custom model binders</span></span>

<span data-ttu-id="f022a-347">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="f022a-347">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="f022a-348">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="f022a-348">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="f022a-349">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-349">Manual model binding</span></span>

<span data-ttu-id="f022a-350">Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="f022a-350">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="f022a-351">Metoda je definována v obou třídách `ControllerBase` i `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="f022a-351">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="f022a-352">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="f022a-352">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="f022a-353">Metoda vrátí `false`, pokud se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="f022a-353">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="f022a-354">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-354">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="f022a-355">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-355">[FromServices] attribute</span></span>

<span data-ttu-id="f022a-356">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="f022a-356">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="f022a-357">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-357">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="f022a-358">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="f022a-358">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f022a-359">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="f022a-359">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f022a-360">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f022a-360">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f022a-361">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="f022a-361">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="f022a-362">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f022a-362">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="f022a-363">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-363">What is Model binding</span></span>

<span data-ttu-id="f022a-364">Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-364">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="f022a-365">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-365">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="f022a-366">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="f022a-366">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="f022a-367">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="f022a-367">Model binding automates this process.</span></span> <span data-ttu-id="f022a-368">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="f022a-368">The model binding system:</span></span>

* <span data-ttu-id="f022a-369">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="f022a-369">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="f022a-370">Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="f022a-370">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="f022a-371">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="f022a-371">Converts string data to .NET types.</span></span>
* <span data-ttu-id="f022a-372">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="f022a-372">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="f022a-373">Příklad</span><span class="sxs-lookup"><span data-stu-id="f022a-373">Example</span></span>

<span data-ttu-id="f022a-374">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f022a-374">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="f022a-375">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="f022a-375">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="f022a-376">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="f022a-376">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="f022a-377">Najde první parametr `GetByID`, celé číslo s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="f022a-377">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="f022a-378">Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".</span><span class="sxs-lookup"><span data-stu-id="f022a-378">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="f022a-379">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="f022a-379">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="f022a-380">Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f022a-380">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="f022a-381">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="f022a-381">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="f022a-382">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f022a-382">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="f022a-383">Převede řetězec "true" na Boolean `true`.</span><span class="sxs-lookup"><span data-stu-id="f022a-383">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="f022a-384">Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="f022a-384">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="f022a-385">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-385">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="f022a-386">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="f022a-386">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="f022a-387">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f022a-387">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="f022a-388">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="f022a-388">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="f022a-389">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="f022a-389">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="f022a-390">Cíle</span><span class="sxs-lookup"><span data-stu-id="f022a-390">Targets</span></span>

<span data-ttu-id="f022a-391">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="f022a-391">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="f022a-392">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="f022a-392">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="f022a-393">Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="f022a-393">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="f022a-394">Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="f022a-394">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="f022a-395">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-395">[BindProperty] attribute</span></span>

<span data-ttu-id="f022a-396">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f022a-396">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="f022a-397">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-397">[BindProperties] attribute</span></span>

<span data-ttu-id="f022a-398">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="f022a-398">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="f022a-399">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="f022a-399">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="f022a-400">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="f022a-400">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="f022a-401">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="f022a-401">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="f022a-402">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="f022a-402">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="f022a-403">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="f022a-403">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="f022a-404">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-404">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="f022a-405">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:</span><span class="sxs-lookup"><span data-stu-id="f022a-405">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="f022a-406">Zdroje</span><span class="sxs-lookup"><span data-stu-id="f022a-406">Sources</span></span>

<span data-ttu-id="f022a-407">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="f022a-407">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="f022a-408">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="f022a-408">Form fields</span></span>
1. <span data-ttu-id="f022a-409">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="f022a-409">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="f022a-410">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="f022a-410">Route data</span></span>
1. <span data-ttu-id="f022a-411">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="f022a-411">Query string parameters</span></span>
1. <span data-ttu-id="f022a-412">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="f022a-412">Uploaded files</span></span>

<span data-ttu-id="f022a-413">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="f022a-413">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="f022a-414">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="f022a-414">There are a few exceptions:</span></span>

* <span data-ttu-id="f022a-415">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-415">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="f022a-416">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="f022a-416">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="f022a-417">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="f022a-417">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="f022a-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f022a-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="f022a-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="f022a-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="f022a-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="f022a-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="f022a-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="f022a-423">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="f022a-423">These attributes:</span></span>

* <span data-ttu-id="f022a-424">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-424">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="f022a-425">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f022a-425">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="f022a-426">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-426">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="f022a-427">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-427">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="f022a-428">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-428">[FromBody] attribute</span></span>

<span data-ttu-id="f022a-429">Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-429">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="f022a-430">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="f022a-430">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="f022a-431">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f022a-431">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="f022a-432">Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují.</span><span class="sxs-lookup"><span data-stu-id="f022a-432">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="f022a-433">Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="f022a-433">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="f022a-434">Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="f022a-434">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="f022a-435">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f022a-435">In the preceding example:</span></span>

* <span data-ttu-id="f022a-436">Atribut `[FromQuery]` je ignorován.</span><span class="sxs-lookup"><span data-stu-id="f022a-436">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="f022a-437">Vlastnost `Breed` není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f022a-437">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="f022a-438">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="f022a-438">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="f022a-439">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.</span><span class="sxs-lookup"><span data-stu-id="f022a-439">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="f022a-440">Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="f022a-440">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="f022a-441">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="f022a-441">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="f022a-442">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f022a-442">Additional sources</span></span>

<span data-ttu-id="f022a-443">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="f022a-443">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="f022a-444">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="f022a-444">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="f022a-445">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="f022a-445">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="f022a-446">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="f022a-446">To get data from a new source:</span></span>

* <span data-ttu-id="f022a-447">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="f022a-447">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="f022a-448">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="f022a-448">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="f022a-449">Zaregistrujte třídu factory v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f022a-449">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="f022a-450">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f022a-450">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="f022a-451">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f022a-451">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="f022a-452">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-452">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="f022a-453">Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.</span><span class="sxs-lookup"><span data-stu-id="f022a-453">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="f022a-454">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-454">No source for a model property</span></span>

<span data-ttu-id="f022a-455">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="f022a-455">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="f022a-456">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="f022a-456">The property is set to null or a default value:</span></span>

* <span data-ttu-id="f022a-457">Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.</span><span class="sxs-lookup"><span data-stu-id="f022a-457">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="f022a-458">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="f022a-458">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="f022a-459">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="f022a-459">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="f022a-460">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="f022a-460">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="f022a-461">Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.</span><span class="sxs-lookup"><span data-stu-id="f022a-461">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="f022a-462">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f022a-462">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="f022a-463">Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-463">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="f022a-464">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="f022a-464">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="f022a-465">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="f022a-465">Type conversion errors</span></span>

<span data-ttu-id="f022a-466">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="f022a-466">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="f022a-467">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="f022a-467">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="f022a-468">V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="f022a-468">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="f022a-469">Na stránce Razor znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="f022a-469">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="f022a-470">Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-470">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="f022a-471">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="f022a-471">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="f022a-472">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="f022a-472">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="f022a-473">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="f022a-473">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="f022a-474">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f022a-474">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="f022a-475">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f022a-475">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="f022a-476">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f022a-476">The invalid input does appear in an error message.</span></span> <span data-ttu-id="f022a-477">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="f022a-477">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="f022a-478">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="f022a-478">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="f022a-479">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="f022a-479">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="f022a-480">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="f022a-480">Simple types</span></span>

<span data-ttu-id="f022a-481">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="f022a-481">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="f022a-482">Datový typ Boolean</span><span class="sxs-lookup"><span data-stu-id="f022a-482">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="f022a-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="f022a-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="f022a-484">Char</span><span class="sxs-lookup"><span data-stu-id="f022a-484">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="f022a-485">Hodnotu</span><span class="sxs-lookup"><span data-stu-id="f022a-485">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="f022a-486">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="f022a-486">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="f022a-487">Notaci</span><span class="sxs-lookup"><span data-stu-id="f022a-487">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="f022a-488">Klepat</span><span class="sxs-lookup"><span data-stu-id="f022a-488">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="f022a-489">Vytváření</span><span class="sxs-lookup"><span data-stu-id="f022a-489">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="f022a-490">Hlavních</span><span class="sxs-lookup"><span data-stu-id="f022a-490">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="f022a-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="f022a-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="f022a-492">Konkrétní</span><span class="sxs-lookup"><span data-stu-id="f022a-492">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="f022a-493">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="f022a-493">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="f022a-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="f022a-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="f022a-495">Uri</span><span class="sxs-lookup"><span data-stu-id="f022a-495">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="f022a-496">Verze</span><span class="sxs-lookup"><span data-stu-id="f022a-496">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="f022a-497">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="f022a-497">Complex types</span></span>

<span data-ttu-id="f022a-498">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="f022a-498">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="f022a-499">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f022a-499">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="f022a-500">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-500">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="f022a-501">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-501">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="f022a-502">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="f022a-502">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="f022a-503">Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-503">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="f022a-504">Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f022a-504">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="f022a-505">Předpokládejme například, že komplexní typ je následující třída `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="f022a-505">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="f022a-506">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="f022a-506">Prefix = parameter name</span></span>

<span data-ttu-id="f022a-507">Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="f022a-507">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="f022a-508">Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-508">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="f022a-509">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-509">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="f022a-510">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="f022a-510">Prefix = property name</span></span>

<span data-ttu-id="f022a-511">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="f022a-511">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="f022a-512">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-512">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f022a-513">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-513">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="f022a-514">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="f022a-514">Custom prefix</span></span>

<span data-ttu-id="f022a-515">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="f022a-515">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="f022a-516">Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="f022a-516">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="f022a-517">Pokud se nenajde, vyhledá `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-517">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="f022a-518">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="f022a-518">Attributes for complex type targets</span></span>

<span data-ttu-id="f022a-519">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="f022a-519">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="f022a-520">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-520">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="f022a-521">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="f022a-521">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="f022a-522">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="f022a-522">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="f022a-523">Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="f022a-523">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="f022a-524">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-524">[BindRequired] attribute</span></span>

<span data-ttu-id="f022a-525">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-525">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f022a-526">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="f022a-526">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="f022a-527">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-527">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="f022a-528">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-528">[BindNever] attribute</span></span>

<span data-ttu-id="f022a-529">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-529">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="f022a-530">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-530">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="f022a-531">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-531">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="f022a-532">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-532">[Bind] attribute</span></span>

<span data-ttu-id="f022a-533">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="f022a-533">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="f022a-534">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="f022a-534">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="f022a-535">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:</span><span class="sxs-lookup"><span data-stu-id="f022a-535">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="f022a-536">V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:</span><span class="sxs-lookup"><span data-stu-id="f022a-536">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="f022a-537">Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="f022a-537">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="f022a-538">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="f022a-538">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="f022a-539">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="f022a-539">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="f022a-540">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="f022a-540">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="f022a-541">Kolekce</span><span class="sxs-lookup"><span data-stu-id="f022a-541">Collections</span></span>

<span data-ttu-id="f022a-542">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-542">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f022a-543">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-543">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f022a-544">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-544">For example:</span></span>

* <span data-ttu-id="f022a-545">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-545">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="f022a-546">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="f022a-546">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="f022a-547">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="f022a-547">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="f022a-548">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-548">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f022a-549">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="f022a-549">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="f022a-550">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="f022a-550">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="f022a-551">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="f022a-551">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="f022a-552">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="f022a-552">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="f022a-553">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="f022a-553">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="f022a-554">Slovníky</span><span class="sxs-lookup"><span data-stu-id="f022a-554">Dictionaries</span></span>

<span data-ttu-id="f022a-555">U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="f022a-555">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="f022a-556">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="f022a-556">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="f022a-557">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-557">For example:</span></span>

* <span data-ttu-id="f022a-558">Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-558">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="f022a-559">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="f022a-559">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="f022a-560">Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="f022a-560">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="f022a-561">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="f022a-561">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="f022a-562">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="f022a-562">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="f022a-563">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="f022a-563">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="f022a-564">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="f022a-564">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="f022a-565">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="f022a-565">Treat values as invariant culture.</span></span>
* <span data-ttu-id="f022a-566">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="f022a-566">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="f022a-567">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="f022a-567">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="f022a-568">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="f022a-568">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="f022a-569">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="f022a-569">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="f022a-570">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="f022a-570">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="f022a-571">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="f022a-571">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="f022a-572">Nahraďte [hodnotu jazykové verze](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="f022a-572">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="f022a-573">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="f022a-573">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="f022a-574">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="f022a-574">Special data types</span></span>

<span data-ttu-id="f022a-575">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="f022a-575">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="f022a-576">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="f022a-576">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="f022a-577">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f022a-577">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="f022a-578">Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.</span><span class="sxs-lookup"><span data-stu-id="f022a-578">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="f022a-579">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="f022a-579">CancellationToken</span></span>

<span data-ttu-id="f022a-580">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="f022a-580">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="f022a-581">FormCollection</span><span class="sxs-lookup"><span data-stu-id="f022a-581">FormCollection</span></span>

<span data-ttu-id="f022a-582">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="f022a-582">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="f022a-583">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="f022a-583">Input formatters</span></span>

<span data-ttu-id="f022a-584">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="f022a-584">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="f022a-585">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="f022a-585">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="f022a-586">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="f022a-586">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="f022a-587">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="f022a-587">You can add other formatters for other content types.</span></span>

<span data-ttu-id="f022a-588">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f022a-588">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="f022a-589">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="f022a-589">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="f022a-590">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="f022a-590">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="f022a-591">Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="f022a-591">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="f022a-592">V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="f022a-592">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="f022a-593">Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="f022a-593">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="f022a-594">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="f022a-594">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="f022a-595">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-595">Exclude specified types from model binding</span></span>

<span data-ttu-id="f022a-596">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="f022a-596">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="f022a-597">`ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="f022a-597">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="f022a-598">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="f022a-598">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="f022a-599">Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="f022a-599">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f022a-600">Například pro zákaz vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="f022a-600">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="f022a-601">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>.</span><span class="sxs-lookup"><span data-stu-id="f022a-601">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f022a-602">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="f022a-602">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="f022a-603">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="f022a-603">Custom model binders</span></span>

<span data-ttu-id="f022a-604">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="f022a-604">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="f022a-605">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="f022a-605">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="f022a-606">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f022a-606">Manual model binding</span></span>

<span data-ttu-id="f022a-607">Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="f022a-607">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="f022a-608">Metoda je definována v obou třídách `ControllerBase` i `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="f022a-608">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="f022a-609">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="f022a-609">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="f022a-610">Metoda vrátí `false`, pokud se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="f022a-610">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="f022a-611">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="f022a-611">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="f022a-612">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="f022a-612">[FromServices] attribute</span></span>

<span data-ttu-id="f022a-613">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="f022a-613">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="f022a-614">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="f022a-614">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="f022a-615">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="f022a-615">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f022a-616">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="f022a-616">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f022a-617">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f022a-617">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
