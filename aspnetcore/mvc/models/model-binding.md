---
title: Vazba modelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vazba modelu v ASP.NET Core funguje a jak přizpůsobit jeho chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: 2e604cd1869ea077fc0465df91ec083b9db83763
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768967"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="8b817-103">Vazba modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b817-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b817-104">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="8b817-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="8b817-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b817-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="8b817-106">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-106">What is Model binding</span></span>

<span data-ttu-id="8b817-107">Řadiče a Razor stránky fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="8b817-108">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="8b817-109">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="8b817-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="8b817-110">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="8b817-110">Model binding automates this process.</span></span> <span data-ttu-id="8b817-111">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="8b817-111">The model binding system:</span></span>

* <span data-ttu-id="8b817-112">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="8b817-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="8b817-113">Poskytuje data pro řadiče a Razor stránky v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="8b817-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="8b817-114">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="8b817-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="8b817-115">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="8b817-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="8b817-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="8b817-116">Example</span></span>

<span data-ttu-id="8b817-117">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="8b817-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="8b817-118">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="8b817-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="8b817-119">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="8b817-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="8b817-120">Vyhledá první parametr typu `GetByID`Integer s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="8b817-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="8b817-121">Vyhledá z dostupných zdrojů v požadavku HTTP a vyhledá `id` v datech směrování = "2".</span><span class="sxs-lookup"><span data-stu-id="8b817-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="8b817-122">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="8b817-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="8b817-123">Najde další parametr `GetByID`logického názvu `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="8b817-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="8b817-124">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="8b817-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="8b817-125">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8b817-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="8b817-126">Převede řetězec "true" na logickou `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b817-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="8b817-127">Rozhraní potom `GetById` zavolá metodu, která předá 2 `id` pro parametr a `true` pro `dogsOnly` parametr.</span><span class="sxs-lookup"><span data-stu-id="8b817-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="8b817-128">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="8b817-129">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="8b817-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="8b817-130">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b817-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="8b817-131">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="8b817-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="8b817-132">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="8b817-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="8b817-133">Cíle</span><span class="sxs-lookup"><span data-stu-id="8b817-133">Targets</span></span>

<span data-ttu-id="8b817-134">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="8b817-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="8b817-135">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="8b817-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="8b817-136">Parametry metody obslužné Razor rutiny stránky, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="8b817-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="8b817-137">Veřejné vlastnosti řadiče nebo `PageModel` třídy, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="8b817-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="8b817-138">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-138">[BindProperty] attribute</span></span>

<span data-ttu-id="8b817-139">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b817-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="8b817-140">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-140">[BindProperties] attribute</span></span>

<span data-ttu-id="8b817-141">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="8b817-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="8b817-142">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="8b817-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="8b817-143">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="8b817-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="8b817-144">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="8b817-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="8b817-145">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="8b817-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="8b817-146">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b817-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="8b817-147">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="8b817-148">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte `SupportsGet` vlastnost na `true`:</span><span class="sxs-lookup"><span data-stu-id="8b817-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="8b817-149">zdroje</span><span class="sxs-lookup"><span data-stu-id="8b817-149">Sources</span></span>

<span data-ttu-id="8b817-150">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="8b817-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="8b817-151">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="8b817-151">Form fields</span></span>
1. <span data-ttu-id="8b817-152">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="8b817-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="8b817-153">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="8b817-153">Route data</span></span>
1. <span data-ttu-id="8b817-154">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="8b817-154">Query string parameters</span></span>
1. <span data-ttu-id="8b817-155">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="8b817-155">Uploaded files</span></span>

<span data-ttu-id="8b817-156">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="8b817-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="8b817-157">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="8b817-157">There are a few exceptions:</span></span>

* <span data-ttu-id="8b817-158">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="8b817-159">Nahrané soubory jsou vázány pouze na cílové `IFormFile` typy `IEnumerable<IFormFile>`, které implementují nebo.</span><span class="sxs-lookup"><span data-stu-id="8b817-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="8b817-160">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="8b817-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="8b817-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)– Načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8b817-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="8b817-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)– Načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="8b817-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="8b817-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)– Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="8b817-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="8b817-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)– Načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="8b817-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)– Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="8b817-166">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="8b817-166">These attributes:</span></span>

* <span data-ttu-id="8b817-167">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="8b817-168">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b817-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="8b817-169">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b817-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="8b817-170">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="8b817-171">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-171">[FromBody] attribute</span></span>

<span data-ttu-id="8b817-172">Použijte `[FromBody]` atribut pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="8b817-173">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="8b817-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="8b817-174">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="8b817-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="8b817-175">Při `[FromBody]` použití na parametr komplexního typu jsou všechny zdrojové atributy vazby použité na jeho vlastnosti ignorovány.</span><span class="sxs-lookup"><span data-stu-id="8b817-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="8b817-176">Například následující `Create` akce určuje, že jeho `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="8b817-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="8b817-177">`Pet` Třída určuje, že jeho `Breed` vlastnost je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="8b817-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="8b817-178">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-178">In the preceding example:</span></span>

* <span data-ttu-id="8b817-179">`[FromQuery]` Atribut je ignorován.</span><span class="sxs-lookup"><span data-stu-id="8b817-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="8b817-180">`Breed` Vlastnost není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8b817-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="8b817-181">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="8b817-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="8b817-182">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění `Breed` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="8b817-183">Neplatí `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="8b817-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="8b817-184">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro navázání `[FromBody]` dalších parametrů.</span><span class="sxs-lookup"><span data-stu-id="8b817-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="8b817-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b817-185">Additional sources</span></span>

<span data-ttu-id="8b817-186">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="8b817-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="8b817-187">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="8b817-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="8b817-188">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="8b817-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="8b817-189">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="8b817-189">To get data from a new source:</span></span>

* <span data-ttu-id="8b817-190">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="8b817-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="8b817-191">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="8b817-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="8b817-192">Zaregistrujte třídu factory `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="8b817-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="8b817-193">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="8b817-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="8b817-194">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8b817-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="8b817-195">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="8b817-196">Chcete-li jej nastavit jako první v seznamu, `Insert(0, new CookieValueProviderFactory())` zavolejte místo `Add`.</span><span class="sxs-lookup"><span data-stu-id="8b817-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="8b817-197">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-197">No source for a model property</span></span>

<span data-ttu-id="8b817-198">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="8b817-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="8b817-199">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8b817-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="8b817-200">Jednoduché typy s možnou hodnotou `null`null jsou nastaveny na.</span><span class="sxs-lookup"><span data-stu-id="8b817-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="8b817-201">Typy hodnot, které nejsou null, jsou `default(T)`nastaveny na.</span><span class="sxs-lookup"><span data-stu-id="8b817-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="8b817-202">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="8b817-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="8b817-203">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="8b817-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="8b817-204">Pole jsou nastavena na `Array.Empty<T>()`, s výjimkou toho, `byte[]` že `null`pole jsou nastavena na.</span><span class="sxs-lookup"><span data-stu-id="8b817-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="8b817-205">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte [`[BindRequired]`](#bindrequired-attribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="8b817-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="8b817-206">Všimněte si, `[BindRequired]` že toto chování se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="8b817-207">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="8b817-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="8b817-208">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="8b817-208">Type conversion errors</span></span>

<span data-ttu-id="8b817-209">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="8b817-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="8b817-210">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="8b817-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="8b817-211">V kontroleru rozhraní API, který má `[ApiController]` atribut, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="8b817-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="8b817-212">Na Razor stránce znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="8b817-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="8b817-213">Ověřování na straně klienta zachytí nejvíc chybná data, která by byla jinak Razor odeslána na formulář stránky.</span><span class="sxs-lookup"><span data-stu-id="8b817-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="8b817-214">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="8b817-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="8b817-215">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="8b817-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="8b817-216">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="8b817-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="8b817-217">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="8b817-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="8b817-218">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b817-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="8b817-219">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="8b817-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="8b817-220">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="8b817-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="8b817-221">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="8b817-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="8b817-222">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="8b817-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="8b817-223">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="8b817-223">Simple types</span></span>

<span data-ttu-id="8b817-224">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="8b817-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="8b817-225">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="8b817-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="8b817-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="8b817-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="8b817-227">Char</span><span class="sxs-lookup"><span data-stu-id="8b817-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="8b817-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="8b817-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="8b817-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="8b817-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="8b817-230">Notaci</span><span class="sxs-lookup"><span data-stu-id="8b817-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="8b817-231">Klepat</span><span class="sxs-lookup"><span data-stu-id="8b817-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="8b817-232">Výčet</span><span class="sxs-lookup"><span data-stu-id="8b817-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="8b817-233">Hlavních</span><span class="sxs-lookup"><span data-stu-id="8b817-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="8b817-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="8b817-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="8b817-235">Single</span><span class="sxs-lookup"><span data-stu-id="8b817-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="8b817-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="8b817-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="8b817-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="8b817-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="8b817-238">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="8b817-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="8b817-239">Verze</span><span class="sxs-lookup"><span data-stu-id="8b817-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="8b817-240">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="8b817-240">Complex types</span></span>

<span data-ttu-id="8b817-241">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="8b817-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="8b817-242">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b817-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="8b817-243">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="8b817-244">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="8b817-245">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="8b817-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="8b817-246">Pro svázání s `PageModel` veřejnou vlastností je předpona názvem veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="8b817-247">Některé atributy mají `Prefix` vlastnost, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="8b817-248">Předpokládejme například, že komplexní typ je následující `Instructor` třída:</span><span class="sxs-lookup"><span data-stu-id="8b817-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="8b817-249">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="8b817-249">Prefix = parameter name</span></span>

<span data-ttu-id="8b817-250">Pokud je model, který chcete svázat, parametr s `instructorToUpdate`názvem:</span><span class="sxs-lookup"><span data-stu-id="8b817-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="8b817-251">Vazba modelu začne prohledáním zdrojů klíče `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="8b817-252">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="8b817-253">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="8b817-253">Prefix = property name</span></span>

<span data-ttu-id="8b817-254">Pokud je model, který chcete svázat, vlastnost s `Instructor` názvem kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="8b817-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="8b817-255">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="8b817-256">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="8b817-257">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="8b817-257">Custom prefix</span></span>

<span data-ttu-id="8b817-258">Pokud je model, který má být svázán, parametr `instructorToUpdate` pojmenovaný a `Bind` atribut určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="8b817-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="8b817-259">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="8b817-260">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="8b817-261">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="8b817-261">Attributes for complex type targets</span></span>

<span data-ttu-id="8b817-262">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="8b817-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="8b817-263">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="8b817-264">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b817-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="8b817-265">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="8b817-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="8b817-266">Viz také diskuze o `[Required]` atributu v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="8b817-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="8b817-267">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-267">[BindRequired] attribute</span></span>

<span data-ttu-id="8b817-268">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="8b817-269">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="8b817-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="8b817-270">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="8b817-271">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-271">[BindNever] attribute</span></span>

<span data-ttu-id="8b817-272">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="8b817-273">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="8b817-274">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="8b817-275">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-275">[Bind] attribute</span></span>

<span data-ttu-id="8b817-276">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="8b817-277">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="8b817-278">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo `Instructor` metody akce vázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="8b817-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="8b817-279">V následujícím příkladu jsou při volání `Instructor` `OnPost` metody svázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="8b817-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="8b817-280">`[Bind]` Atribut lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="8b817-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="8b817-281">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="8b817-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="8b817-282">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto `[Bind]` atributu.</span><span class="sxs-lookup"><span data-stu-id="8b817-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="8b817-283">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="8b817-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="8b817-284">Kolekce</span><span class="sxs-lookup"><span data-stu-id="8b817-284">Collections</span></span>

<span data-ttu-id="8b817-285">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="8b817-286">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="8b817-287">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-287">For example:</span></span>

* <span data-ttu-id="8b817-288">Předpokládejme, že parametr, který má být svázán, `selectedCourses`je pole s názvem:</span><span class="sxs-lookup"><span data-stu-id="8b817-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="8b817-289">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="8b817-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="8b817-290">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="8b817-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="8b817-291">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="8b817-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="8b817-292">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="8b817-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="8b817-293">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="8b817-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="8b817-294">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="8b817-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="8b817-295">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="8b817-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="8b817-296">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="8b817-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="8b817-297">Slovníky</span><span class="sxs-lookup"><span data-stu-id="8b817-297">Dictionaries</span></span>

<span data-ttu-id="8b817-298">V `Dictionary` případě cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="8b817-299">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="8b817-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-300">For example:</span></span>

* <span data-ttu-id="8b817-301">Předpokládejme, že cílový parametr je `Dictionary<int, string>` pojmenovaný `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="8b817-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="8b817-302">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="8b817-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="8b817-303">Pro všechny předchozí ukázkové formáty předává vazba modelu do `selectedCourses` parametru slovník dvou položek:</span><span class="sxs-lookup"><span data-stu-id="8b817-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="8b817-304">selectedCourses ["1050"] = "chemie"</span><span class="sxs-lookup"><span data-stu-id="8b817-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="8b817-305">selectedCourses ["2000"] = "ekonomické"</span><span class="sxs-lookup"><span data-stu-id="8b817-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="8b817-306">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="8b817-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="8b817-307">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="8b817-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="8b817-308">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="8b817-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="8b817-309">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="8b817-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="8b817-310">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="8b817-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="8b817-311">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="8b817-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="8b817-312">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="8b817-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="8b817-313">Zdědit z<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="8b817-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="8b817-314">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="8b817-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="8b817-315">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="8b817-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="8b817-316">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="8b817-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="8b817-317">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="8b817-317">Special data types</span></span>

<span data-ttu-id="8b817-318">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="8b817-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="8b817-319">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="8b817-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="8b817-320">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="8b817-321">Podporováno je `IEnumerable<IFormFile>` také pro více souborů.</span><span class="sxs-lookup"><span data-stu-id="8b817-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="8b817-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="8b817-322">CancellationToken</span></span>

<span data-ttu-id="8b817-323">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="8b817-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="8b817-324">Formulářcollection</span><span class="sxs-lookup"><span data-stu-id="8b817-324">FormCollection</span></span>

<span data-ttu-id="8b817-325">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="8b817-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="8b817-326">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="8b817-326">Input formatters</span></span>

<span data-ttu-id="8b817-327">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="8b817-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="8b817-328">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="8b817-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="8b817-329">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="8b817-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="8b817-330">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="8b817-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="8b817-331">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="8b817-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="8b817-332">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="8b817-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="8b817-333">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="8b817-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="8b817-334">Nainstalujte balíček `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet.</span><span class="sxs-lookup"><span data-stu-id="8b817-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="8b817-335">V `Startup.ConfigureServices`, zavolejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="8b817-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="8b817-336">Použijte `Consumes` atribut na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="8b817-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="8b817-337">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="8b817-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="8b817-338">Přizpůsobení vazby modelu pomocí vstupních formátovacích prvků</span><span class="sxs-lookup"><span data-stu-id="8b817-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="8b817-339">Vstupní formátovací modul má plnou zodpovědnost za čtení dat z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="8b817-340">Chcete-li tento proces přizpůsobit, nakonfigurujte rozhraní API používaná vstupním formátovacím modulem.</span><span class="sxs-lookup"><span data-stu-id="8b817-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="8b817-341">Tato část popisuje, jak přizpůsobit `System.Text.Json`vstupní formátovací modul založený na základě vlastního typu s názvem. `ObjectId`</span><span class="sxs-lookup"><span data-stu-id="8b817-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="8b817-342">Vezměte v úvahu následující model, který obsahuje vlastní `ObjectId` vlastnost s `Id`názvem:</span><span class="sxs-lookup"><span data-stu-id="8b817-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="8b817-343">Chcete-li přizpůsobit proces vazby modelu při `System.Text.Json`použití, vytvořte třídu odvozenou <xref:System.Text.Json.Serialization.JsonConverter%601>z:</span><span class="sxs-lookup"><span data-stu-id="8b817-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="8b817-344">Chcete-li použít vlastní převaděč, použijte <xref:System.Text.Json.Serialization.JsonConverterAttribute> atribut na typ.</span><span class="sxs-lookup"><span data-stu-id="8b817-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="8b817-345">V následujícím příkladu je `ObjectId` typ nakonfigurován s `ObjectIdConverter` jako svůj vlastní převaděč:</span><span class="sxs-lookup"><span data-stu-id="8b817-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="8b817-346">Další informace najdete v tématu [jak psát vlastní převaděče](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="8b817-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="8b817-347">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="8b817-348">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="8b817-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="8b817-349">Můžete přizpůsobit `ModelMetadata` přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="8b817-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="8b817-350">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="8b817-351">Chcete-li zakázat vazbu modelu ve všech modelech zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> do `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b817-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b817-352">Například pro zakázání vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="8b817-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="8b817-353">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> do. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8b817-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b817-354">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="8b817-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="8b817-355">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="8b817-355">Custom model binders</span></span>

<span data-ttu-id="8b817-356">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů `[ModelBinder]` a pomocí atributu ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="8b817-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="8b817-357">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="8b817-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="8b817-358">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-358">Manual model binding</span></span> 

<span data-ttu-id="8b817-359">Vazbu modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="8b817-360">Metoda je definována v obou `ControllerBase` `PageModel` třídách i.</span><span class="sxs-lookup"><span data-stu-id="8b817-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="8b817-361">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="8b817-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="8b817-362">Metoda vrátí `false` , zda se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="8b817-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="8b817-363">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="8b817-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>používá zprostředkovatele hodnot k získání dat z textu formuláře, řetězce dotazu a dat směrování.</span><span class="sxs-lookup"><span data-stu-id="8b817-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="8b817-365">`TryUpdateModelAsync`je obvykle:</span><span class="sxs-lookup"><span data-stu-id="8b817-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="8b817-366">Používá se Razor pro stránky a aplikace MVC pomocí řadičů a zobrazení k tomu, aby se zabránilo přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="8b817-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="8b817-367">Nepoužívá se s webovým rozhraním API, pokud se nevyužívá pro data formulářů, řetězce dotazů a data směrování.</span><span class="sxs-lookup"><span data-stu-id="8b817-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="8b817-368">Koncové body webového rozhraní API, které využívají [Formátovací moduly vstupu](#input-formatters) JSON k deserializaci těla požadavku do objektu.</span><span class="sxs-lookup"><span data-stu-id="8b817-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="8b817-369">Další informace najdete v tématu [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="8b817-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="8b817-370">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-370">[FromServices] attribute</span></span>

<span data-ttu-id="8b817-371">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="8b817-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="8b817-372">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="8b817-373">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="8b817-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8b817-374">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="8b817-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b817-375">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="8b817-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b817-376">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="8b817-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="8b817-377">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8b817-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="8b817-378">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-378">What is Model binding</span></span>

<span data-ttu-id="8b817-379">Řadiče a Razor stránky fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="8b817-380">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="8b817-381">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="8b817-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="8b817-382">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="8b817-382">Model binding automates this process.</span></span> <span data-ttu-id="8b817-383">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="8b817-383">The model binding system:</span></span>

* <span data-ttu-id="8b817-384">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="8b817-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="8b817-385">Poskytuje data pro řadiče a Razor stránky v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="8b817-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="8b817-386">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="8b817-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="8b817-387">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="8b817-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="8b817-388">Příklad</span><span class="sxs-lookup"><span data-stu-id="8b817-388">Example</span></span>

<span data-ttu-id="8b817-389">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="8b817-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="8b817-390">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="8b817-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="8b817-391">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="8b817-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="8b817-392">Vyhledá první parametr typu `GetByID`Integer s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="8b817-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="8b817-393">Vyhledá z dostupných zdrojů v požadavku HTTP a vyhledá `id` v datech směrování = "2".</span><span class="sxs-lookup"><span data-stu-id="8b817-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="8b817-394">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="8b817-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="8b817-395">Najde další parametr `GetByID`logického názvu `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="8b817-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="8b817-396">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="8b817-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="8b817-397">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8b817-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="8b817-398">Převede řetězec "true" na logickou `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b817-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="8b817-399">Rozhraní potom `GetById` zavolá metodu, která předá 2 `id` pro parametr a `true` pro `dogsOnly` parametr.</span><span class="sxs-lookup"><span data-stu-id="8b817-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="8b817-400">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="8b817-401">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="8b817-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="8b817-402">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8b817-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="8b817-403">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="8b817-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="8b817-404">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="8b817-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="8b817-405">Cíle</span><span class="sxs-lookup"><span data-stu-id="8b817-405">Targets</span></span>

<span data-ttu-id="8b817-406">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="8b817-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="8b817-407">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="8b817-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="8b817-408">Parametry metody obslužné Razor rutiny stránky, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="8b817-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="8b817-409">Veřejné vlastnosti řadiče nebo `PageModel` třídy, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="8b817-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="8b817-410">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-410">[BindProperty] attribute</span></span>

<span data-ttu-id="8b817-411">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8b817-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="8b817-412">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-412">[BindProperties] attribute</span></span>

<span data-ttu-id="8b817-413">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="8b817-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="8b817-414">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="8b817-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="8b817-415">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="8b817-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="8b817-416">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="8b817-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="8b817-417">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="8b817-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="8b817-418">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b817-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="8b817-419">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="8b817-420">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte `SupportsGet` vlastnost na `true`:</span><span class="sxs-lookup"><span data-stu-id="8b817-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="8b817-421">zdroje</span><span class="sxs-lookup"><span data-stu-id="8b817-421">Sources</span></span>

<span data-ttu-id="8b817-422">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="8b817-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="8b817-423">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="8b817-423">Form fields</span></span>
1. <span data-ttu-id="8b817-424">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="8b817-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="8b817-425">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="8b817-425">Route data</span></span>
1. <span data-ttu-id="8b817-426">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="8b817-426">Query string parameters</span></span>
1. <span data-ttu-id="8b817-427">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="8b817-427">Uploaded files</span></span>

<span data-ttu-id="8b817-428">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="8b817-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="8b817-429">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="8b817-429">There are a few exceptions:</span></span>

* <span data-ttu-id="8b817-430">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="8b817-431">Nahrané soubory jsou vázány pouze na cílové `IFormFile` typy `IEnumerable<IFormFile>`, které implementují nebo.</span><span class="sxs-lookup"><span data-stu-id="8b817-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="8b817-432">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="8b817-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="8b817-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)– Načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8b817-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="8b817-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)– Načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="8b817-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="8b817-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)– Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="8b817-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="8b817-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)– Načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="8b817-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)– Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="8b817-438">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="8b817-438">These attributes:</span></span>

* <span data-ttu-id="8b817-439">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="8b817-440">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b817-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="8b817-441">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b817-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="8b817-442">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="8b817-443">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-443">[FromBody] attribute</span></span>

<span data-ttu-id="8b817-444">Použijte `[FromBody]` atribut pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="8b817-445">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="8b817-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="8b817-446">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="8b817-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="8b817-447">Při `[FromBody]` použití na parametr komplexního typu jsou všechny zdrojové atributy vazby použité na jeho vlastnosti ignorovány.</span><span class="sxs-lookup"><span data-stu-id="8b817-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="8b817-448">Například následující `Create` akce určuje, že jeho `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="8b817-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="8b817-449">`Pet` Třída určuje, že jeho `Breed` vlastnost je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="8b817-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="8b817-450">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8b817-450">In the preceding example:</span></span>

* <span data-ttu-id="8b817-451">`[FromQuery]` Atribut je ignorován.</span><span class="sxs-lookup"><span data-stu-id="8b817-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="8b817-452">`Breed` Vlastnost není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8b817-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="8b817-453">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="8b817-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="8b817-454">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění `Breed` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="8b817-455">Neplatí `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="8b817-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="8b817-456">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro navázání `[FromBody]` dalších parametrů.</span><span class="sxs-lookup"><span data-stu-id="8b817-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="8b817-457">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b817-457">Additional sources</span></span>

<span data-ttu-id="8b817-458">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="8b817-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="8b817-459">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="8b817-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="8b817-460">Například můžete chtít data ze souborů cookie nebo stavu relace.</span><span class="sxs-lookup"><span data-stu-id="8b817-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="8b817-461">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="8b817-461">To get data from a new source:</span></span>

* <span data-ttu-id="8b817-462">Vytvořte třídu, která implementuje `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="8b817-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="8b817-463">Vytvořte třídu, která implementuje `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="8b817-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="8b817-464">Zaregistrujte třídu factory `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="8b817-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="8b817-465">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="8b817-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="8b817-466">Zde je registrační kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8b817-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="8b817-467">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="8b817-468">Chcete-li jej nastavit jako první v seznamu, `Insert(0, new CookieValueProviderFactory())` zavolejte místo `Add`.</span><span class="sxs-lookup"><span data-stu-id="8b817-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="8b817-469">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-469">No source for a model property</span></span>

<span data-ttu-id="8b817-470">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="8b817-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="8b817-471">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8b817-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="8b817-472">Jednoduché typy s možnou hodnotou `null`null jsou nastaveny na.</span><span class="sxs-lookup"><span data-stu-id="8b817-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="8b817-473">Typy hodnot, které nejsou null, jsou `default(T)`nastaveny na.</span><span class="sxs-lookup"><span data-stu-id="8b817-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="8b817-474">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="8b817-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="8b817-475">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="8b817-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="8b817-476">Pole jsou nastavena na `Array.Empty<T>()`, s výjimkou toho, `byte[]` že `null`pole jsou nastavena na.</span><span class="sxs-lookup"><span data-stu-id="8b817-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="8b817-477">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte [`[BindRequired]`](#bindrequired-attribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="8b817-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="8b817-478">Všimněte si, `[BindRequired]` že toto chování se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle žádosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="8b817-479">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="8b817-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="8b817-480">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="8b817-480">Type conversion errors</span></span>

<span data-ttu-id="8b817-481">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="8b817-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="8b817-482">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="8b817-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="8b817-483">V kontroleru rozhraní API, který má `[ApiController]` atribut, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="8b817-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="8b817-484">Na Razor stránce znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="8b817-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="8b817-485">Ověřování na straně klienta zachytí nejvíc chybná data, která by byla jinak Razor odeslána na formulář stránky.</span><span class="sxs-lookup"><span data-stu-id="8b817-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="8b817-486">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="8b817-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="8b817-487">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="8b817-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="8b817-488">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="8b817-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="8b817-489">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="8b817-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="8b817-490">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8b817-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="8b817-491">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="8b817-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="8b817-492">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="8b817-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="8b817-493">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="8b817-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="8b817-494">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="8b817-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="8b817-495">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="8b817-495">Simple types</span></span>

<span data-ttu-id="8b817-496">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="8b817-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="8b817-497">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="8b817-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="8b817-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="8b817-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="8b817-499">Char</span><span class="sxs-lookup"><span data-stu-id="8b817-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="8b817-500">DateTime</span><span class="sxs-lookup"><span data-stu-id="8b817-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="8b817-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="8b817-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="8b817-502">Notaci</span><span class="sxs-lookup"><span data-stu-id="8b817-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="8b817-503">Klepat</span><span class="sxs-lookup"><span data-stu-id="8b817-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="8b817-504">Výčet</span><span class="sxs-lookup"><span data-stu-id="8b817-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="8b817-505">Hlavních</span><span class="sxs-lookup"><span data-stu-id="8b817-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="8b817-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="8b817-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="8b817-507">Single</span><span class="sxs-lookup"><span data-stu-id="8b817-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="8b817-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="8b817-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="8b817-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="8b817-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="8b817-510">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="8b817-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="8b817-511">Verze</span><span class="sxs-lookup"><span data-stu-id="8b817-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="8b817-512">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="8b817-512">Complex types</span></span>

<span data-ttu-id="8b817-513">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="8b817-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="8b817-514">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b817-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="8b817-515">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="8b817-516">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="8b817-517">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="8b817-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="8b817-518">Pro svázání s `PageModel` veřejnou vlastností je předpona názvem veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="8b817-519">Některé atributy mají `Prefix` vlastnost, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8b817-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="8b817-520">Předpokládejme například, že komplexní typ je následující `Instructor` třída:</span><span class="sxs-lookup"><span data-stu-id="8b817-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="8b817-521">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="8b817-521">Prefix = parameter name</span></span>

<span data-ttu-id="8b817-522">Pokud je model, který chcete svázat, parametr s `instructorToUpdate`názvem:</span><span class="sxs-lookup"><span data-stu-id="8b817-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="8b817-523">Vazba modelu začne prohledáním zdrojů klíče `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="8b817-524">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="8b817-525">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="8b817-525">Prefix = property name</span></span>

<span data-ttu-id="8b817-526">Pokud je model, který chcete svázat, vlastnost s `Instructor` názvem kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="8b817-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="8b817-527">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="8b817-528">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="8b817-529">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="8b817-529">Custom prefix</span></span>

<span data-ttu-id="8b817-530">Pokud je model, který má být svázán, parametr `instructorToUpdate` pojmenovaný a `Bind` atribut určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="8b817-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="8b817-531">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="8b817-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="8b817-532">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="8b817-533">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="8b817-533">Attributes for complex type targets</span></span>

<span data-ttu-id="8b817-534">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="8b817-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="8b817-535">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="8b817-536">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b817-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="8b817-537">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="8b817-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="8b817-538">Viz také diskuze o `[Required]` atributu v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="8b817-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="8b817-539">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-539">[BindRequired] attribute</span></span>

<span data-ttu-id="8b817-540">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="8b817-541">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="8b817-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="8b817-542">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="8b817-543">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-543">[BindNever] attribute</span></span>

<span data-ttu-id="8b817-544">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="8b817-545">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="8b817-546">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="8b817-547">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-547">[Bind] attribute</span></span>

<span data-ttu-id="8b817-548">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="8b817-549">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="8b817-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="8b817-550">V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo `Instructor` metody akce vázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="8b817-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="8b817-551">V následujícím příkladu jsou při volání `Instructor` `OnPost` metody svázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="8b817-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="8b817-552">`[Bind]` Atribut lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="8b817-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="8b817-553">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="8b817-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="8b817-554">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto `[Bind]` atributu.</span><span class="sxs-lookup"><span data-stu-id="8b817-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="8b817-555">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="8b817-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="8b817-556">Kolekce</span><span class="sxs-lookup"><span data-stu-id="8b817-556">Collections</span></span>

<span data-ttu-id="8b817-557">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="8b817-558">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="8b817-559">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-559">For example:</span></span>

* <span data-ttu-id="8b817-560">Předpokládejme, že parametr, který má být svázán, `selectedCourses`je pole s názvem:</span><span class="sxs-lookup"><span data-stu-id="8b817-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="8b817-561">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="8b817-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="8b817-562">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="8b817-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="8b817-563">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="8b817-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="8b817-564">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="8b817-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="8b817-565">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="8b817-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="8b817-566">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="8b817-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="8b817-567">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="8b817-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="8b817-568">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="8b817-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="8b817-569">Slovníky</span><span class="sxs-lookup"><span data-stu-id="8b817-569">Dictionaries</span></span>

<span data-ttu-id="8b817-570">V `Dictionary` případě cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="8b817-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="8b817-571">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8b817-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="8b817-572">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-572">For example:</span></span>

* <span data-ttu-id="8b817-573">Předpokládejme, že cílový parametr je `Dictionary<int, string>` pojmenovaný `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="8b817-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="8b817-574">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="8b817-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="8b817-575">Pro všechny předchozí ukázkové formáty předává vazba modelu do `selectedCourses` parametru slovník dvou položek:</span><span class="sxs-lookup"><span data-stu-id="8b817-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="8b817-576">selectedCourses ["1050"] = "chemie"</span><span class="sxs-lookup"><span data-stu-id="8b817-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="8b817-577">selectedCourses ["2000"] = "ekonomické"</span><span class="sxs-lookup"><span data-stu-id="8b817-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="8b817-578">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="8b817-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="8b817-579">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="8b817-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="8b817-580">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="8b817-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="8b817-581">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="8b817-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="8b817-582">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="8b817-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="8b817-583">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="8b817-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="8b817-584">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="8b817-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="8b817-585">Zdědit z<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="8b817-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="8b817-586">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="8b817-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="8b817-587">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="8b817-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="8b817-588">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="8b817-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="8b817-589">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="8b817-589">Special data types</span></span>

<span data-ttu-id="8b817-590">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="8b817-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="8b817-591">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="8b817-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="8b817-592">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b817-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="8b817-593">Podporováno je `IEnumerable<IFormFile>` také pro více souborů.</span><span class="sxs-lookup"><span data-stu-id="8b817-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="8b817-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="8b817-594">CancellationToken</span></span>

<span data-ttu-id="8b817-595">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="8b817-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="8b817-596">Formulářcollection</span><span class="sxs-lookup"><span data-stu-id="8b817-596">FormCollection</span></span>

<span data-ttu-id="8b817-597">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="8b817-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="8b817-598">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="8b817-598">Input formatters</span></span>

<span data-ttu-id="8b817-599">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="8b817-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="8b817-600">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="8b817-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="8b817-601">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="8b817-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="8b817-602">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="8b817-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="8b817-603">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="8b817-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="8b817-604">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="8b817-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="8b817-605">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="8b817-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="8b817-606">Nainstalujte balíček `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet.</span><span class="sxs-lookup"><span data-stu-id="8b817-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="8b817-607">V `Startup.ConfigureServices`, zavolejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="8b817-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="8b817-608">Použijte `Consumes` atribut na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="8b817-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="8b817-609">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="8b817-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="8b817-610">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="8b817-611">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="8b817-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="8b817-612">Můžete přizpůsobit `ModelMetadata` přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="8b817-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="8b817-613">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="8b817-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="8b817-614">Chcete-li zakázat vazbu modelu ve všech modelech zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> do `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8b817-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b817-615">Například pro zakázání vazby modelu u všech modelů typu `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="8b817-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="8b817-616">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> do. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8b817-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b817-617">Chcete-li například zakázat ověřování vlastností typu `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="8b817-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="8b817-618">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="8b817-618">Custom model binders</span></span>

<span data-ttu-id="8b817-619">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů `[ModelBinder]` a pomocí atributu ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="8b817-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="8b817-620">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="8b817-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="8b817-621">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="8b817-621">Manual model binding</span></span>

<span data-ttu-id="8b817-622">Vazbu modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="8b817-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="8b817-623">Metoda je definována v obou `ControllerBase` `PageModel` třídách i.</span><span class="sxs-lookup"><span data-stu-id="8b817-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="8b817-624">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="8b817-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="8b817-625">Metoda vrátí `false` , zda se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="8b817-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="8b817-626">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="8b817-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="8b817-627">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="8b817-627">[FromServices] attribute</span></span>

<span data-ttu-id="8b817-628">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="8b817-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="8b817-629">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="8b817-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="8b817-630">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="8b817-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8b817-631">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="8b817-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b817-632">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="8b817-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
