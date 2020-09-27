---
title: Vazba modelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vazba modelu v ASP.NET Core funguje a jak přizpůsobit jeho chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393883"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="eb90c-103">Vazba modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb90c-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb90c-104">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="eb90c-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb90c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="eb90c-106">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-106">What is Model binding</span></span>

<span data-ttu-id="eb90c-107">Řadiče a Razor stránky fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="eb90c-108">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="eb90c-109">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="eb90c-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="eb90c-110">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="eb90c-110">Model binding automates this process.</span></span> <span data-ttu-id="eb90c-111">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-111">The model binding system:</span></span>

* <span data-ttu-id="eb90c-112">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="eb90c-113">Poskytuje data pro řadiče a Razor stránky v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="eb90c-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="eb90c-114">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="eb90c-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="eb90c-115">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="eb90c-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="eb90c-116">Example</span></span>

<span data-ttu-id="eb90c-117">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="eb90c-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="eb90c-118">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="eb90c-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="eb90c-119">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="eb90c-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="eb90c-120">Vyhledá první parametr typu `GetByID` Integer s názvem `id` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="eb90c-121">Vyhledá z dostupných zdrojů v požadavku HTTP a vyhledá `id` v datech směrování = "2".</span><span class="sxs-lookup"><span data-stu-id="eb90c-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="eb90c-122">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="eb90c-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="eb90c-123">Najde další parametr `GetByID` logického názvu `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="eb90c-124">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="eb90c-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="eb90c-125">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="eb90c-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="eb90c-126">Převede řetězec "true" na logickou hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="eb90c-127">Rozhraní potom zavolá `GetById` metodu, která předá 2 pro `id` parametr a `true` pro `dogsOnly` parametr.</span><span class="sxs-lookup"><span data-stu-id="eb90c-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="eb90c-128">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="eb90c-129">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="eb90c-130">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="eb90c-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="eb90c-131">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="eb90c-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="eb90c-132">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="eb90c-133">Targets</span><span class="sxs-lookup"><span data-stu-id="eb90c-133">Targets</span></span>

<span data-ttu-id="eb90c-134">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="eb90c-135">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="eb90c-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="eb90c-136">Parametry Razor metody obslužné rutiny stránky, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="eb90c-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="eb90c-137">Veřejné vlastnosti řadiče nebo `PageModel` třídy, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="eb90c-138">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-138">[BindProperty] attribute</span></span>

<span data-ttu-id="eb90c-139">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="eb90c-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="eb90c-140">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-140">[BindProperties] attribute</span></span>

<span data-ttu-id="eb90c-141">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="eb90c-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="eb90c-142">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="eb90c-143">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="eb90c-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="eb90c-144">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="eb90c-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="eb90c-145">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="eb90c-146">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="eb90c-147">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="eb90c-148">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte `SupportsGet` vlastnost na `true` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="eb90c-149">zdroje</span><span class="sxs-lookup"><span data-stu-id="eb90c-149">Sources</span></span>

<span data-ttu-id="eb90c-150">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="eb90c-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="eb90c-151">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="eb90c-151">Form fields</span></span>
1. <span data-ttu-id="eb90c-152">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="eb90c-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="eb90c-153">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="eb90c-153">Route data</span></span>
1. <span data-ttu-id="eb90c-154">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="eb90c-154">Query string parameters</span></span>
1. <span data-ttu-id="eb90c-155">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="eb90c-155">Uploaded files</span></span>

<span data-ttu-id="eb90c-156">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="eb90c-157">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="eb90c-157">There are a few exceptions:</span></span>

* <span data-ttu-id="eb90c-158">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="eb90c-159">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="eb90c-160">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="eb90c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – Načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="eb90c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – Načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="eb90c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="eb90c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="eb90c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – Načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="eb90c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="eb90c-166">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-166">These attributes:</span></span>

* <span data-ttu-id="eb90c-167">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="eb90c-168">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="eb90c-169">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb90c-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="eb90c-170">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="eb90c-171">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-171">[FromBody] attribute</span></span>

<span data-ttu-id="eb90c-172">Použijte `[FromBody]` atribut pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="eb90c-173">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="eb90c-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="eb90c-174">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="eb90c-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="eb90c-175">Při `[FromBody]` použití na parametr komplexního typu jsou všechny zdrojové atributy vazby použité na jeho vlastnosti ignorovány.</span><span class="sxs-lookup"><span data-stu-id="eb90c-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="eb90c-176">Například následující `Create` akce určuje, že jeho `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="eb90c-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="eb90c-177">`Pet`Třída určuje, že jeho `Breed` vlastnost je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="eb90c-178">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-178">In the preceding example:</span></span>

* <span data-ttu-id="eb90c-179">`[FromQuery]`Atribut je ignorován.</span><span class="sxs-lookup"><span data-stu-id="eb90c-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="eb90c-180">`Breed`Vlastnost není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="eb90c-181">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="eb90c-182">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění `Breed` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="eb90c-183">Neplatí `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="eb90c-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="eb90c-184">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro navázání dalších `[FromBody]` parametrů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="eb90c-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb90c-185">Additional sources</span></span>

<span data-ttu-id="eb90c-186">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="eb90c-187">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="eb90c-188">Například můžete chtít data ze cookie stavu s nebo relace.</span><span class="sxs-lookup"><span data-stu-id="eb90c-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="eb90c-189">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="eb90c-189">To get data from a new source:</span></span>

* <span data-ttu-id="eb90c-190">Vytvořte třídu, která implementuje `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="eb90c-191">Vytvořte třídu, která implementuje `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="eb90c-192">Zaregistrujte třídu factory v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="eb90c-193">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získá hodnoty z cookie s.</span><span class="sxs-lookup"><span data-stu-id="eb90c-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="eb90c-194">Zde je registrační kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="eb90c-195">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="eb90c-196">Chcete-li jej nastavit jako první v seznamu, zavolejte `Insert(0, new CookieValueProviderFactory())` místo `Add` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="eb90c-197">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-197">No source for a model property</span></span>

<span data-ttu-id="eb90c-198">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="eb90c-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="eb90c-199">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="eb90c-200">Jednoduché typy s možnou hodnotou null jsou nastaveny na `null` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="eb90c-201">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="eb90c-202">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="eb90c-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="eb90c-203">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="eb90c-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="eb90c-204">Pole jsou nastavena na `Array.Empty<T>()` , s výjimkou toho, že `byte[]` pole jsou nastavena na `null` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="eb90c-205">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte [`[BindRequired]`](#bindrequired-attribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="eb90c-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="eb90c-206">Všimněte si, že toto `[BindRequired]` chování se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="eb90c-207">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="eb90c-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="eb90c-208">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="eb90c-208">Type conversion errors</span></span>

<span data-ttu-id="eb90c-209">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="eb90c-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="eb90c-210">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="eb90c-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="eb90c-211">V kontroleru rozhraní API, který má `[ApiController]` atribut, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="eb90c-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="eb90c-212">Na Razor stránce znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="eb90c-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="eb90c-213">Ověřování na straně klienta zachytí nejvíc chybná data, která by byla jinak odeslána na Razor formulář stránky.</span><span class="sxs-lookup"><span data-stu-id="eb90c-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="eb90c-214">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="eb90c-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="eb90c-215">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="eb90c-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="eb90c-216">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="eb90c-217">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="eb90c-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="eb90c-218">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="eb90c-219">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="eb90c-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="eb90c-220">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="eb90c-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="eb90c-221">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="eb90c-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="eb90c-222">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="eb90c-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="eb90c-223">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-223">Simple types</span></span>

<span data-ttu-id="eb90c-224">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="eb90c-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="eb90c-225">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="eb90c-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="eb90c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="eb90c-227">Char</span><span class="sxs-lookup"><span data-stu-id="eb90c-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="eb90c-228">Datum a čas</span><span class="sxs-lookup"><span data-stu-id="eb90c-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="eb90c-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="eb90c-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="eb90c-230">Notaci</span><span class="sxs-lookup"><span data-stu-id="eb90c-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="eb90c-231">Klepat</span><span class="sxs-lookup"><span data-stu-id="eb90c-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="eb90c-232">Výčet</span><span class="sxs-lookup"><span data-stu-id="eb90c-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="eb90c-233">Hlavních</span><span class="sxs-lookup"><span data-stu-id="eb90c-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="eb90c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="eb90c-235">Jednoduché</span><span class="sxs-lookup"><span data-stu-id="eb90c-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="eb90c-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="eb90c-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="eb90c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="eb90c-238">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="eb90c-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="eb90c-239">Verze</span><span class="sxs-lookup"><span data-stu-id="eb90c-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="eb90c-240">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-240">Complex types</span></span>

<span data-ttu-id="eb90c-241">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="eb90c-242">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="eb90c-243">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="eb90c-244">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="eb90c-245">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="eb90c-246">Pro svázání s `PageModel` veřejnou vlastností je předpona názvem veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="eb90c-247">Některé atributy mají `Prefix` vlastnost, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="eb90c-248">Předpokládejme například, že komplexní typ je následující `Instructor` Třída:</span><span class="sxs-lookup"><span data-stu-id="eb90c-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="eb90c-249">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="eb90c-249">Prefix = parameter name</span></span>

<span data-ttu-id="eb90c-250">Pokud je model, který chcete svázat, parametr s názvem `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="eb90c-251">Vazba modelu začne prohledáním zdrojů klíče `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="eb90c-252">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="eb90c-253">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="eb90c-253">Prefix = property name</span></span>

<span data-ttu-id="eb90c-254">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="eb90c-255">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb90c-256">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="eb90c-257">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="eb90c-257">Custom prefix</span></span>

<span data-ttu-id="eb90c-258">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a `Bind` atribut určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="eb90c-259">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb90c-260">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="eb90c-261">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="eb90c-261">Attributes for complex type targets</span></span>

<span data-ttu-id="eb90c-262">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="eb90c-263">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="eb90c-264">Neovlivňují ***vstupní*** formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb90c-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="eb90c-265">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="eb90c-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="eb90c-266">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-266">[Bind] attribute</span></span>

<span data-ttu-id="eb90c-267">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="eb90c-268">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="eb90c-269">`[Bind]` nemá ***vliv na*** vstupní formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="eb90c-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="eb90c-270">V následujícím příkladu `Instructor` jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="eb90c-271">V následujícím příkladu `Instructor` jsou při volání metody svázány pouze zadané vlastnosti modelu `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="eb90c-272">`[Bind]`Atribut lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="eb90c-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="eb90c-273">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="eb90c-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="eb90c-274">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto `[Bind]` atributu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="eb90c-275">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="eb90c-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="eb90c-276">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-276">[BindRequired] attribute</span></span>

<span data-ttu-id="eb90c-277">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb90c-278">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="eb90c-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="eb90c-279">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="eb90c-280">Viz také diskuze o `[Required]` atributu v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb90c-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="eb90c-281">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-281">[BindNever] attribute</span></span>

<span data-ttu-id="eb90c-282">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb90c-283">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="eb90c-284">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="eb90c-285">Kolekce</span><span class="sxs-lookup"><span data-stu-id="eb90c-285">Collections</span></span>

<span data-ttu-id="eb90c-286">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb90c-287">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb90c-288">Příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-288">For example:</span></span>

* <span data-ttu-id="eb90c-289">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="eb90c-290">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-290">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="eb90c-291">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="eb90c-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="eb90c-292">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="eb90c-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb90c-293">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="eb90c-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="eb90c-294">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="eb90c-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="eb90c-295">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="eb90c-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="eb90c-296">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="eb90c-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="eb90c-297">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="eb90c-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="eb90c-298">Slovníky</span><span class="sxs-lookup"><span data-stu-id="eb90c-298">Dictionaries</span></span>

<span data-ttu-id="eb90c-299">V případě `Dictionary` cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb90c-300">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb90c-301">Příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-301">For example:</span></span>

* <span data-ttu-id="eb90c-302">Předpokládejme, že cílový parametr je `Dictionary<int, string>` pojmenovaný `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="eb90c-303">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-303">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="eb90c-304">Pro všechny předchozí ukázkové formáty předává vazba modelu do parametru slovník dvou položek `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb90c-305">selectedCourses ["1050"] = "chemie"</span><span class="sxs-lookup"><span data-stu-id="eb90c-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="eb90c-306">selectedCourses ["2000"] = "ekonomické"</span><span class="sxs-lookup"><span data-stu-id="eb90c-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="eb90c-307">Vazba konstruktoru a typy záznamů</span><span class="sxs-lookup"><span data-stu-id="eb90c-307">Constructor binding and record types</span></span>

<span data-ttu-id="eb90c-308">Vazba modelu vyžaduje, aby komplexní typy měly konstruktor bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="eb90c-309">`System.Text.Json`Formátovací moduly `Newtonsoft.Json` vstupu na bázi i podporují deserializaci tříd, které nemají konstruktor bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="eb90c-310">C# 9 zavádí typy záznamů, což je skvělý způsob, jak stručně reprezentovat data v síti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="eb90c-311">ASP.NET Core přidává podporu pro vazbu modelu a ověřování typů záznamů s jedním konstruktorem:</span><span class="sxs-lookup"><span data-stu-id="eb90c-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="eb90c-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="eb90c-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="eb90c-313">Při ověřování typů záznamů modul runtime vyhledává metadata ověření konkrétně u parametrů, nikoli u vlastností.</span><span class="sxs-lookup"><span data-stu-id="eb90c-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="eb90c-314">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="eb90c-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="eb90c-315">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="eb90c-316">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="eb90c-317">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="eb90c-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="eb90c-318">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="eb90c-319">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="eb90c-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="eb90c-320">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="eb90c-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="eb90c-321">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="eb90c-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="eb90c-322">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="eb90c-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="eb90c-323">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="eb90c-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="eb90c-324">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="eb90c-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="eb90c-325">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-325">Special data types</span></span>

<span data-ttu-id="eb90c-326">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="eb90c-327">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="eb90c-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="eb90c-328">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="eb90c-329">Podporováno je také `IEnumerable<IFormFile>` pro více souborů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="eb90c-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="eb90c-330">CancellationToken</span></span>

<span data-ttu-id="eb90c-331">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="eb90c-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="eb90c-332">Formulářcollection</span><span class="sxs-lookup"><span data-stu-id="eb90c-332">FormCollection</span></span>

<span data-ttu-id="eb90c-333">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="eb90c-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="eb90c-334">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="eb90c-334">Input formatters</span></span>

<span data-ttu-id="eb90c-335">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="eb90c-336">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="eb90c-337">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="eb90c-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="eb90c-338">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="eb90c-339">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="eb90c-340">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="eb90c-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="eb90c-341">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="eb90c-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="eb90c-342">Nainstalujte `Microsoft.AspNetCore.Mvc.Formatters.Xml` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb90c-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="eb90c-343">V `Startup.ConfigureServices` , zavolejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="eb90c-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="eb90c-344">Použijte `Consumes` atribut na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="eb90c-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="eb90c-345">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="eb90c-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="eb90c-346">Přizpůsobení vazby modelu pomocí vstupních formátovacích prvků</span><span class="sxs-lookup"><span data-stu-id="eb90c-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="eb90c-347">Vstupní formátovací modul má plnou zodpovědnost za čtení dat z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="eb90c-348">Chcete-li tento proces přizpůsobit, nakonfigurujte rozhraní API používaná vstupním formátovacím modulem.</span><span class="sxs-lookup"><span data-stu-id="eb90c-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="eb90c-349">Tato část popisuje, jak přizpůsobit `System.Text.Json` vstupní formátovací modul založený na základě vlastního typu s názvem `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="eb90c-350">Vezměte v úvahu následující model, který obsahuje vlastní `ObjectId` vlastnost s názvem `Id` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="eb90c-351">Chcete-li přizpůsobit proces vazby modelu při použití `System.Text.Json` , vytvořte třídu odvozenou z <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="eb90c-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="eb90c-352">Chcete-li použít vlastní převaděč, použijte <xref:System.Text.Json.Serialization.JsonConverterAttribute> atribut na typ.</span><span class="sxs-lookup"><span data-stu-id="eb90c-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="eb90c-353">V následujícím příkladu `ObjectId` je typ nakonfigurován s `ObjectIdConverter` jako svůj vlastní převaděč:</span><span class="sxs-lookup"><span data-stu-id="eb90c-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="eb90c-354">Další informace najdete v tématu [jak psát vlastní převaděče](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="eb90c-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="eb90c-355">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="eb90c-356">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="eb90c-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="eb90c-357">Můžete přizpůsobit `ModelMetadata` přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="eb90c-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="eb90c-358">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="eb90c-359">Chcete-li zakázat vazbu modelu ve všech modelech zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb90c-360">Například pro zakázání vazby modelu u všech modelů typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="eb90c-361">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb90c-362">Chcete-li například zakázat ověřování vlastností typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="eb90c-363">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="eb90c-363">Custom model binders</span></span>

<span data-ttu-id="eb90c-364">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí `[ModelBinder]` atributu ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="eb90c-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="eb90c-365">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb90c-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="eb90c-366">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-366">Manual model binding</span></span> 

<span data-ttu-id="eb90c-367">Vazbu modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="eb90c-368">Metoda je definována v obou `ControllerBase` `PageModel` třídách i.</span><span class="sxs-lookup"><span data-stu-id="eb90c-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="eb90c-369">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="eb90c-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="eb90c-370">Metoda vrátí, `false` zda se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="eb90c-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="eb90c-371">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="eb90c-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  používá zprostředkovatele hodnot k získání dat z textu formuláře, řetězce dotazu a dat směrování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="eb90c-373">`TryUpdateModelAsync` je obvykle:</span><span class="sxs-lookup"><span data-stu-id="eb90c-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="eb90c-374">Používá se pro Razor stránky a aplikace MVC pomocí řadičů a zobrazení k tomu, aby se zabránilo přeúčtování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-374">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="eb90c-375">Nepoužívá se s webovým rozhraním API, pokud se nevyužívá pro data formulářů, řetězce dotazů a data směrování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="eb90c-376">Koncové body webového rozhraní API, které využívají [Formátovací moduly vstupu](#input-formatters) JSON k deserializaci těla požadavku do objektu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="eb90c-377">Další informace najdete v tématu [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="eb90c-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="eb90c-378">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-378">[FromServices] attribute</span></span>

<span data-ttu-id="eb90c-379">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="eb90c-380">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="eb90c-381">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eb90c-382">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="eb90c-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb90c-383">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb90c-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb90c-384">Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="eb90c-385">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb90c-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="eb90c-386">Co je vazba modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-386">What is Model binding</span></span>

<span data-ttu-id="eb90c-387">Řadiče a Razor stránky fungují s daty, která pocházejí z požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-387">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="eb90c-388">Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="eb90c-389">Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám.</span><span class="sxs-lookup"><span data-stu-id="eb90c-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="eb90c-390">Vazba modelu automatizuje tento proces.</span><span class="sxs-lookup"><span data-stu-id="eb90c-390">Model binding automates this process.</span></span> <span data-ttu-id="eb90c-391">Systém vazby modelů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-391">The model binding system:</span></span>

* <span data-ttu-id="eb90c-392">Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="eb90c-393">Poskytuje data pro řadiče a Razor stránky v parametrech metod a veřejných vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="eb90c-393">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="eb90c-394">Převádí řetězcová data na typy .NET.</span><span class="sxs-lookup"><span data-stu-id="eb90c-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="eb90c-395">Aktualizuje vlastnosti komplexních typů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="eb90c-396">Příklad</span><span class="sxs-lookup"><span data-stu-id="eb90c-396">Example</span></span>

<span data-ttu-id="eb90c-397">Předpokládejme, že máte následující metodu akce:</span><span class="sxs-lookup"><span data-stu-id="eb90c-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="eb90c-398">A aplikace obdrží požadavek s touto adresou URL:</span><span class="sxs-lookup"><span data-stu-id="eb90c-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="eb90c-399">Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:</span><span class="sxs-lookup"><span data-stu-id="eb90c-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="eb90c-400">Vyhledá první parametr typu `GetByID` Integer s názvem `id` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="eb90c-401">Vyhledá z dostupných zdrojů v požadavku HTTP a vyhledá `id` v datech směrování = "2".</span><span class="sxs-lookup"><span data-stu-id="eb90c-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="eb90c-402">Převede řetězec "2" na celé číslo 2.</span><span class="sxs-lookup"><span data-stu-id="eb90c-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="eb90c-403">Najde další parametr `GetByID` logického názvu `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="eb90c-404">Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true".</span><span class="sxs-lookup"><span data-stu-id="eb90c-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="eb90c-405">U porovnávání názvů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="eb90c-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="eb90c-406">Převede řetězec "true" na logickou hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="eb90c-407">Rozhraní potom zavolá `GetById` metodu, která předá 2 pro `id` parametr a `true` pro `dogsOnly` parametr.</span><span class="sxs-lookup"><span data-stu-id="eb90c-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="eb90c-408">V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="eb90c-409">Cíle mohou být také vlastnostmi komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="eb90c-410">Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="eb90c-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="eb90c-411">Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="eb90c-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="eb90c-412">Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="eb90c-413">Targets</span><span class="sxs-lookup"><span data-stu-id="eb90c-413">Targets</span></span>

<span data-ttu-id="eb90c-414">Vazba modelu se pokusí najít hodnoty pro následující typy cílů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="eb90c-415">Parametry metody akce kontroleru, na kterou je směrován požadavek</span><span class="sxs-lookup"><span data-stu-id="eb90c-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="eb90c-416">Parametry Razor metody obslužné rutiny stránky, na kterou je směrován požadavek.</span><span class="sxs-lookup"><span data-stu-id="eb90c-416">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="eb90c-417">Veřejné vlastnosti řadiče nebo `PageModel` třídy, pokud jsou určeny atributy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="eb90c-418">[BindProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-418">[BindProperty] attribute</span></span>

<span data-ttu-id="eb90c-419">Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="eb90c-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="eb90c-420">[BindProperties] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-420">[BindProperties] attribute</span></span>

<span data-ttu-id="eb90c-421">K dispozici v ASP.NET Core 2,1 a novějším.</span><span class="sxs-lookup"><span data-stu-id="eb90c-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="eb90c-422">Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="eb90c-423">Vazba modelu pro požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="eb90c-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="eb90c-424">Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané.</span><span class="sxs-lookup"><span data-stu-id="eb90c-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="eb90c-425">Obvykle stačí pro požadavek GET parametr ID záznamu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="eb90c-426">ID záznamu slouží k vyhledání položky v databázi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="eb90c-427">Proto není nutné navazovat vlastnost, která obsahuje instanci modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="eb90c-428">Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte `SupportsGet` vlastnost na `true` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="eb90c-429">zdroje</span><span class="sxs-lookup"><span data-stu-id="eb90c-429">Sources</span></span>

<span data-ttu-id="eb90c-430">Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:</span><span class="sxs-lookup"><span data-stu-id="eb90c-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="eb90c-431">Pole formuláře</span><span class="sxs-lookup"><span data-stu-id="eb90c-431">Form fields</span></span>
1. <span data-ttu-id="eb90c-432">Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="eb90c-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="eb90c-433">Směrování dat</span><span class="sxs-lookup"><span data-stu-id="eb90c-433">Route data</span></span>
1. <span data-ttu-id="eb90c-434">Parametry řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="eb90c-434">Query string parameters</span></span>
1. <span data-ttu-id="eb90c-435">Nahrané soubory</span><span class="sxs-lookup"><span data-stu-id="eb90c-435">Uploaded files</span></span>

<span data-ttu-id="eb90c-436">Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="eb90c-437">Existuje několik výjimek:</span><span class="sxs-lookup"><span data-stu-id="eb90c-437">There are a few exceptions:</span></span>

* <span data-ttu-id="eb90c-438">Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="eb90c-439">Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="eb90c-440">Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="eb90c-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – Načte hodnoty z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="eb90c-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – Načte hodnoty z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="eb90c-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="eb90c-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.</span><span class="sxs-lookup"><span data-stu-id="eb90c-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="eb90c-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – Načte hodnoty z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="eb90c-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="eb90c-446">Tyto atributy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-446">These attributes:</span></span>

* <span data-ttu-id="eb90c-447">Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="eb90c-448">Volitelně akceptuje hodnotu názvu modelu v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="eb90c-449">Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb90c-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="eb90c-450">Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="eb90c-451">[FromBody] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-451">[FromBody] attribute</span></span>

<span data-ttu-id="eb90c-452">Použijte `[FromBody]` atribut pro parametr k naplnění vlastností z těla požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="eb90c-453">Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="eb90c-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="eb90c-454">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="eb90c-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="eb90c-455">Při `[FromBody]` použití na parametr komplexního typu jsou všechny zdrojové atributy vazby použité na jeho vlastnosti ignorovány.</span><span class="sxs-lookup"><span data-stu-id="eb90c-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="eb90c-456">Například následující `Create` akce určuje, že jeho `pet` parametr je vyplněný z těla:</span><span class="sxs-lookup"><span data-stu-id="eb90c-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="eb90c-457">`Pet`Třída určuje, že jeho `Breed` vlastnost je naplněná z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="eb90c-458">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-458">In the preceding example:</span></span>

* <span data-ttu-id="eb90c-459">`[FromQuery]`Atribut je ignorován.</span><span class="sxs-lookup"><span data-stu-id="eb90c-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="eb90c-460">`Breed`Vlastnost není naplněna z parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="eb90c-461">Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="eb90c-462">Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění `Breed` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="eb90c-463">Neplatí `[FromBody]` pro více než jeden parametr na metodu Action.</span><span class="sxs-lookup"><span data-stu-id="eb90c-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="eb90c-464">Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro navázání dalších `[FromBody]` parametrů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="eb90c-465">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb90c-465">Additional sources</span></span>

<span data-ttu-id="eb90c-466">Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-466">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="eb90c-467">Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="eb90c-468">Například můžete chtít data ze cookie stavu s nebo relace.</span><span class="sxs-lookup"><span data-stu-id="eb90c-468">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="eb90c-469">Načtení dat z nového zdroje:</span><span class="sxs-lookup"><span data-stu-id="eb90c-469">To get data from a new source:</span></span>

* <span data-ttu-id="eb90c-470">Vytvořte třídu, která implementuje `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="eb90c-471">Vytvořte třídu, která implementuje `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="eb90c-472">Zaregistrujte třídu factory v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="eb90c-473">Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získá hodnoty z cookie s.</span><span class="sxs-lookup"><span data-stu-id="eb90c-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="eb90c-474">Zde je registrační kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="eb90c-475">Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="eb90c-476">Chcete-li jej nastavit jako první v seznamu, zavolejte `Insert(0, new CookieValueProviderFactory())` místo `Add` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-476">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="eb90c-477">Žádný zdroj pro vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-477">No source for a model property</span></span>

<span data-ttu-id="eb90c-478">Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota.</span><span class="sxs-lookup"><span data-stu-id="eb90c-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="eb90c-479">Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="eb90c-480">Jednoduché typy s možnou hodnotou null jsou nastaveny na `null` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="eb90c-481">Typy hodnot, které nejsou null, jsou nastaveny na `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="eb90c-482">Například parametr `int id` je nastaven na hodnotu 0.</span><span class="sxs-lookup"><span data-stu-id="eb90c-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="eb90c-483">Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.</span><span class="sxs-lookup"><span data-stu-id="eb90c-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="eb90c-484">Pole jsou nastavena na `Array.Empty<T>()` , s výjimkou toho, že `byte[]` pole jsou nastavena na `null` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="eb90c-485">Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte [`[BindRequired]`](#bindrequired-attribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="eb90c-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="eb90c-486">Všimněte si, že toto `[BindRequired]` chování se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle žádosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="eb90c-487">Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="eb90c-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="eb90c-488">Chyby konverze typu</span><span class="sxs-lookup"><span data-stu-id="eb90c-488">Type conversion errors</span></span>

<span data-ttu-id="eb90c-489">Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="eb90c-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="eb90c-490">Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.</span><span class="sxs-lookup"><span data-stu-id="eb90c-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="eb90c-491">V kontroleru rozhraní API, který má `[ApiController]` atribut, má neplatný stav modelu za následek automatickou odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="eb90c-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="eb90c-492">Na Razor stránce znovu zobrazte stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="eb90c-492">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="eb90c-493">Ověřování na straně klienta zachytí nejvíc chybná data, která by byla jinak odeslána na Razor formulář stránky.</span><span class="sxs-lookup"><span data-stu-id="eb90c-493">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="eb90c-494">Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód.</span><span class="sxs-lookup"><span data-stu-id="eb90c-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="eb90c-495">Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="eb90c-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="eb90c-496">Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="eb90c-497">V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="eb90c-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="eb90c-498">Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="eb90c-499">V chybové zprávě se zobrazí neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="eb90c-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="eb90c-500">Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.</span><span class="sxs-lookup"><span data-stu-id="eb90c-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="eb90c-501">Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii.</span><span class="sxs-lookup"><span data-stu-id="eb90c-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="eb90c-502">V takovém případě nastavte vlastnost model na řetězec.</span><span class="sxs-lookup"><span data-stu-id="eb90c-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="eb90c-503">Jednoduché typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-503">Simple types</span></span>

<span data-ttu-id="eb90c-504">Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:</span><span class="sxs-lookup"><span data-stu-id="eb90c-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="eb90c-505">Logická hodnota</span><span class="sxs-lookup"><span data-stu-id="eb90c-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="eb90c-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="eb90c-507">Char</span><span class="sxs-lookup"><span data-stu-id="eb90c-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="eb90c-508">Datum a čas</span><span class="sxs-lookup"><span data-stu-id="eb90c-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="eb90c-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="eb90c-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="eb90c-510">Notaci</span><span class="sxs-lookup"><span data-stu-id="eb90c-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="eb90c-511">Klepat</span><span class="sxs-lookup"><span data-stu-id="eb90c-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="eb90c-512">Výčet</span><span class="sxs-lookup"><span data-stu-id="eb90c-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="eb90c-513">Hlavních</span><span class="sxs-lookup"><span data-stu-id="eb90c-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="eb90c-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="eb90c-515">Jednoduché</span><span class="sxs-lookup"><span data-stu-id="eb90c-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="eb90c-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="eb90c-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="eb90c-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb90c-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="eb90c-518">Identifikátor URI</span><span class="sxs-lookup"><span data-stu-id="eb90c-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="eb90c-519">Verze</span><span class="sxs-lookup"><span data-stu-id="eb90c-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="eb90c-520">Komplexní typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-520">Complex types</span></span>

<span data-ttu-id="eb90c-521">Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="eb90c-522">Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="eb90c-523">Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="eb90c-524">Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="eb90c-525">Pro svázání s parametrem je předpona názvem parametru.</span><span class="sxs-lookup"><span data-stu-id="eb90c-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="eb90c-526">Pro svázání s `PageModel` veřejnou vlastností je předpona názvem veřejné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="eb90c-527">Některé atributy mají `Prefix` vlastnost, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="eb90c-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="eb90c-528">Předpokládejme například, že komplexní typ je následující `Instructor` Třída:</span><span class="sxs-lookup"><span data-stu-id="eb90c-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="eb90c-529">Prefix = název parametru</span><span class="sxs-lookup"><span data-stu-id="eb90c-529">Prefix = parameter name</span></span>

<span data-ttu-id="eb90c-530">Pokud je model, který chcete svázat, parametr s názvem `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="eb90c-531">Vazba modelu začne prohledáním zdrojů klíče `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="eb90c-532">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="eb90c-533">Prefix = název vlastnosti</span><span class="sxs-lookup"><span data-stu-id="eb90c-533">Prefix = property name</span></span>

<span data-ttu-id="eb90c-534">Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="eb90c-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="eb90c-535">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb90c-536">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="eb90c-537">Vlastní předpona</span><span class="sxs-lookup"><span data-stu-id="eb90c-537">Custom prefix</span></span>

<span data-ttu-id="eb90c-538">Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a `Bind` atribut určuje `Instructor` jako předponu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="eb90c-539">Vazba modelu začne prohledáním zdrojů klíče `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb90c-540">Pokud se nenajde, vyhledá se `ID` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="eb90c-541">Atributy pro cíle komplexního typu</span><span class="sxs-lookup"><span data-stu-id="eb90c-541">Attributes for complex type targets</span></span>

<span data-ttu-id="eb90c-542">K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="eb90c-543">Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="eb90c-544">Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku.</span><span class="sxs-lookup"><span data-stu-id="eb90c-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="eb90c-545">Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="eb90c-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="eb90c-546">Viz také diskuze o `[Required]` atributu v [ověřování modelu](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb90c-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="eb90c-547">[BindRequired] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-547">[BindRequired] attribute</span></span>

<span data-ttu-id="eb90c-548">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb90c-549">Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba.</span><span class="sxs-lookup"><span data-stu-id="eb90c-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="eb90c-550">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="eb90c-551">[BindNever] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-551">[BindNever] attribute</span></span>

<span data-ttu-id="eb90c-552">Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb90c-553">Zabraňuje vazbě modelu v nastavení vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="eb90c-554">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="eb90c-555">[BIND] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-555">[Bind] attribute</span></span>

<span data-ttu-id="eb90c-556">Lze použít pro třídu nebo parametr metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="eb90c-557">Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="eb90c-558">V následujícím příkladu `Instructor` jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="eb90c-559">V následujícím příkladu `Instructor` jsou při volání metody svázány pouze zadané vlastnosti modelu `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="eb90c-560">`[Bind]`Atribut lze použít k ochraně před přeúčtováním ve scénářích *vytváření* .</span><span class="sxs-lookup"><span data-stu-id="eb90c-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="eb90c-561">Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny.</span><span class="sxs-lookup"><span data-stu-id="eb90c-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="eb90c-562">Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto `[Bind]` atributu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="eb90c-563">Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="eb90c-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="eb90c-564">Kolekce</span><span class="sxs-lookup"><span data-stu-id="eb90c-564">Collections</span></span>

<span data-ttu-id="eb90c-565">Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb90c-566">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb90c-567">Příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-567">For example:</span></span>

* <span data-ttu-id="eb90c-568">Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="eb90c-569">Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-569">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="eb90c-570">Následující formát je podporován pouze v datech formuláře:</span><span class="sxs-lookup"><span data-stu-id="eb90c-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="eb90c-571">Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do `selectedCourses` parametru:</span><span class="sxs-lookup"><span data-stu-id="eb90c-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb90c-572">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="eb90c-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="eb90c-573">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="eb90c-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="eb90c-574">Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou.</span><span class="sxs-lookup"><span data-stu-id="eb90c-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="eb90c-575">Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují.</span><span class="sxs-lookup"><span data-stu-id="eb90c-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="eb90c-576">Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.</span><span class="sxs-lookup"><span data-stu-id="eb90c-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="eb90c-577">Slovníky</span><span class="sxs-lookup"><span data-stu-id="eb90c-577">Dictionaries</span></span>

<span data-ttu-id="eb90c-578">V případě `Dictionary` cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*.</span><span class="sxs-lookup"><span data-stu-id="eb90c-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb90c-579">Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony.</span><span class="sxs-lookup"><span data-stu-id="eb90c-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb90c-580">Příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-580">For example:</span></span>

* <span data-ttu-id="eb90c-581">Předpokládejme, že cílový parametr je `Dictionary<int, string>` pojmenovaný `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="eb90c-582">Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:</span><span class="sxs-lookup"><span data-stu-id="eb90c-582">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="eb90c-583">Pro všechny předchozí ukázkové formáty předává vazba modelu do parametru slovník dvou položek `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb90c-584">selectedCourses ["1050"] = "chemie"</span><span class="sxs-lookup"><span data-stu-id="eb90c-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="eb90c-585">selectedCourses ["2000"] = "ekonomické"</span><span class="sxs-lookup"><span data-stu-id="eb90c-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="eb90c-586">Chování globalizace dat trasy vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="eb90c-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="eb90c-587">Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="eb90c-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="eb90c-588">Považovat hodnoty za invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="eb90c-589">Je očekáváno, že adresy URL jsou invariantní jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="eb90c-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="eb90c-590">Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="eb90c-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="eb90c-591">To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.</span><span class="sxs-lookup"><span data-stu-id="eb90c-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="eb90c-592">Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="eb90c-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="eb90c-593">Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="eb90c-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="eb90c-594">Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="eb90c-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="eb90c-595">Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="eb90c-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="eb90c-596">V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:</span><span class="sxs-lookup"><span data-stu-id="eb90c-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="eb90c-597">Speciální datové typy</span><span class="sxs-lookup"><span data-stu-id="eb90c-597">Special data types</span></span>

<span data-ttu-id="eb90c-598">Existují některé speciální datové typy, které mohou vázání modelů zpracovat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="eb90c-599">IFormFile a IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="eb90c-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="eb90c-600">Nahraný soubor zahrnutý v požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="eb90c-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="eb90c-601">Podporováno je také `IEnumerable<IFormFile>` pro více souborů.</span><span class="sxs-lookup"><span data-stu-id="eb90c-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="eb90c-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="eb90c-602">CancellationToken</span></span>

<span data-ttu-id="eb90c-603">Slouží k zrušení aktivity v asynchronních řadičích.</span><span class="sxs-lookup"><span data-stu-id="eb90c-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="eb90c-604">Formulářcollection</span><span class="sxs-lookup"><span data-stu-id="eb90c-604">FormCollection</span></span>

<span data-ttu-id="eb90c-605">Používá se k načtení všech hodnot z publikovaných dat formuláře.</span><span class="sxs-lookup"><span data-stu-id="eb90c-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="eb90c-606">Vstupní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="eb90c-606">Input formatters</span></span>

<span data-ttu-id="eb90c-607">Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="eb90c-608">Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="eb90c-609">Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON.</span><span class="sxs-lookup"><span data-stu-id="eb90c-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="eb90c-610">Můžete přidat další formátovací moduly pro ostatní typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="eb90c-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="eb90c-611">ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="eb90c-612">Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="eb90c-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="eb90c-613">Chcete-li použít předdefinované vstupní formátovací moduly XML:</span><span class="sxs-lookup"><span data-stu-id="eb90c-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="eb90c-614">Nainstalujte `Microsoft.AspNetCore.Mvc.Formatters.Xml` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb90c-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="eb90c-615">V `Startup.ConfigureServices` , zavolejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="eb90c-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="eb90c-616">Použijte `Consumes` atribut na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.</span><span class="sxs-lookup"><span data-stu-id="eb90c-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="eb90c-617">Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="eb90c-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="eb90c-618">Vyloučit zadané typy z vazby modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="eb90c-619">Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="eb90c-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="eb90c-620">Můžete přizpůsobit `ModelMetadata` přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="eb90c-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="eb90c-621">Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.</span><span class="sxs-lookup"><span data-stu-id="eb90c-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="eb90c-622">Chcete-li zakázat vazbu modelu ve všech modelech zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb90c-623">Například pro zakázání vazby modelu u všech modelů typu `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="eb90c-624">Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb90c-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb90c-625">Chcete-li například zakázat ověřování vlastností typu `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="eb90c-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="eb90c-626">Vlastní pořadače modelů</span><span class="sxs-lookup"><span data-stu-id="eb90c-626">Custom model binders</span></span>

<span data-ttu-id="eb90c-627">Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí `[ModelBinder]` atributu ho vyberete pro daný cíl.</span><span class="sxs-lookup"><span data-stu-id="eb90c-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="eb90c-628">Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb90c-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="eb90c-629">Ruční vazba modelu</span><span class="sxs-lookup"><span data-stu-id="eb90c-629">Manual model binding</span></span>

<span data-ttu-id="eb90c-630">Vazbu modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="eb90c-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="eb90c-631">Metoda je definována v obou `ControllerBase` `PageModel` třídách i.</span><span class="sxs-lookup"><span data-stu-id="eb90c-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="eb90c-632">Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít.</span><span class="sxs-lookup"><span data-stu-id="eb90c-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="eb90c-633">Metoda vrátí, `false` zda se vazba modelu nezdařila.</span><span class="sxs-lookup"><span data-stu-id="eb90c-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="eb90c-634">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="eb90c-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="eb90c-635">[FromServices] – atribut</span><span class="sxs-lookup"><span data-stu-id="eb90c-635">[FromServices] attribute</span></span>

<span data-ttu-id="eb90c-636">Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat.</span><span class="sxs-lookup"><span data-stu-id="eb90c-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="eb90c-637">Nejedná se ale o vazbu dat od poskytovatele hodnot.</span><span class="sxs-lookup"><span data-stu-id="eb90c-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="eb90c-638">Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="eb90c-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eb90c-639">Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.</span><span class="sxs-lookup"><span data-stu-id="eb90c-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb90c-640">Další materiály</span><span class="sxs-lookup"><span data-stu-id="eb90c-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
