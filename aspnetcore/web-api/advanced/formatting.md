---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 8bee4efdae5341ddab5bd3aec278ecfef37f0c08
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082351"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="5c188-103">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="5c188-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="5c188-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5c188-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5c188-105">ASP.NET Core MVC obsahuje integrovanou podporu pro formátování dat odpovědí pomocí pevných formátů nebo v reakci na specifikace klientů.</span><span class="sxs-lookup"><span data-stu-id="5c188-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="5c188-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c188-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="5c188-107">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="5c188-107">Format-Specific Action Results</span></span>

<span data-ttu-id="5c188-108">Některé typy výsledků akce jsou specifické pro konkrétní formát, například `JsonResult` a. `ContentResult`</span><span class="sxs-lookup"><span data-stu-id="5c188-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="5c188-109">Akce mohou vracet konkrétní výsledky, které jsou vždy formátovány určitým způsobem.</span><span class="sxs-lookup"><span data-stu-id="5c188-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="5c188-110">Například vrácení a vrátí data `JsonResult` ve formátu JSON bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="5c188-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="5c188-111">`ContentResult` Stejně tak vrátí návratová data řetězce ve formátu prostého textu (stejně jako jednoduše vrátí řetězec).</span><span class="sxs-lookup"><span data-stu-id="5c188-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="5c188-112">Akce není nutná k vrácení určitého konkrétního typu; MVC podporuje jakoukoli návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="5c188-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="5c188-113">Pokud akce vrátí `IActionResult` implementaci a kontroler dědí z `Controller`, mají vývojáři mnoho pomocných metod, které odpovídají mnoha volbám.</span><span class="sxs-lookup"><span data-stu-id="5c188-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="5c188-114">Výsledky z akcí, které vracejí objekty, které `IActionResult` nejsou typy, budou serializovány pomocí `IOutputFormatter` příslušné implementace.</span><span class="sxs-lookup"><span data-stu-id="5c188-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="5c188-115">Chcete-li vrátit data v konkrétním formátu z kontroleru, který dědí ze `Controller` základní třídy, použijte vestavěnou pomocnou metodu `Json` pro návrat JSON a `Content` pro prostý text.</span><span class="sxs-lookup"><span data-stu-id="5c188-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="5c188-116">Vaše metoda Action by měla vracet buď konkrétní typ výsledku (například, `JsonResult`) nebo. `IActionResult`</span><span class="sxs-lookup"><span data-stu-id="5c188-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="5c188-117">Vracení dat ve formátu JSON:</span><span class="sxs-lookup"><span data-stu-id="5c188-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="5c188-118">Ukázková odpověď z této akce:</span><span class="sxs-lookup"><span data-stu-id="5c188-118">Sample response from this action:</span></span>

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující, že typ obsahu odpovědi je Application/JSON](formatting/_static/json-response.png)

<span data-ttu-id="5c188-120">Všimněte si, že typ obsahu odpovědi je `application/json`zobrazen v seznamu síťových požadavků a v části hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5c188-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="5c188-121">Všimněte si také seznam možností prezentovaných prohlížečem (v tomto případě Microsoft Edge) v hlavičce Accept v části s hlavičkami žádosti.</span><span class="sxs-lookup"><span data-stu-id="5c188-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="5c188-122">Aktuální technika ignoruje tuto hlavičku. pojednává o tom, jak je popsáno níže.</span><span class="sxs-lookup"><span data-stu-id="5c188-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="5c188-123">Chcete-li vrátit data ve formátu prostého textu `Content` , použijte aplikaci `ContentResult` a pomocníka:</span><span class="sxs-lookup"><span data-stu-id="5c188-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="5c188-124">Odpověď z této akce:</span><span class="sxs-lookup"><span data-stu-id="5c188-124">A response from this action:</span></span>

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující, že typ obsahu odpovědi je text/prostý](formatting/_static/text-response.png)

<span data-ttu-id="5c188-126">Všimněte si, že `Content-Type` v tomto případě je `text/plain`vráceno.</span><span class="sxs-lookup"><span data-stu-id="5c188-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="5c188-127">Stejné chování můžete také dosáhnout pouze pomocí typu řetězcové odpovědi:</span><span class="sxs-lookup"><span data-stu-id="5c188-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="5c188-128">Pro netriviální akce s vícenásobnými návratovými typy nebo možnostmi (například různé stavové kódy HTTP založené na výsledku prováděných operací), preferovat `IActionResult` jako návratový typ.</span><span class="sxs-lookup"><span data-stu-id="5c188-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="5c188-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="5c188-129">Content Negotiation</span></span>

<span data-ttu-id="5c188-130">Vyjednávání obsahu (*conneg* pro krátké) proběhne, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="5c188-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="5c188-131">Výchozí formát používaný ASP.NET Core MVC je JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="5c188-132">Vyjednávání obsahu je implementováno `ObjectResult`nástrojem.</span><span class="sxs-lookup"><span data-stu-id="5c188-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="5c188-133">Je také integrováno do výsledků akce specifické pro stav, které byly vráceny z pomocných metod (které jsou založeny `ObjectResult`na).</span><span class="sxs-lookup"><span data-stu-id="5c188-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="5c188-134">Můžete také vrátit typ modelu (třídu, kterou jste definovali jako typ přenosu dat), a rozhraní si ho `ObjectResult` automaticky zalomí za vás.</span><span class="sxs-lookup"><span data-stu-id="5c188-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="5c188-135">Následující metoda akce používá `Ok` pomocné metody a: `NotFound`</span><span class="sxs-lookup"><span data-stu-id="5c188-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="5c188-136">Pokud není požadován jiný formát a server může vracet požadovaný formát, bude vrácena odpověď ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="5c188-137">Pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) , můžete vytvořit požadavek, který obsahuje hlavičku Accept a zadat jiný formát.</span><span class="sxs-lookup"><span data-stu-id="5c188-137">You can use a tool like [Fiddler](https://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="5c188-138">V takovém případě, pokud má server *formátovací modul* , který může vytvořit odpověď v požadovaném formátu, výsledek bude vrácen ve formátu upřednostňovaném pro klienta.</span><span class="sxs-lookup"><span data-stu-id="5c188-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![Konzola Fiddler zobrazující manuálně vytvořený požadavek GET s hodnotou potvrdit hlavičku Application/XML](formatting/_static/fiddler-composer.png)

<span data-ttu-id="5c188-140">Ve výše uvedeném snímku obrazovky byl k vygenerování požadavku použit skladatel Fiddler, který určí `Accept: application/xml`.</span><span class="sxs-lookup"><span data-stu-id="5c188-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="5c188-141">Ve výchozím nastavení ASP.NET Core MVC podporuje pouze JSON, takže i v případě, že je zadán jiný formát, vrácený výsledek je stále ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="5c188-142">V další části se dozvíte, jak přidat další formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="5c188-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="5c188-143">Akce kontroleru mohou vracet POCOs (staré staré objekty CLR). v takovém případě ASP.NET Core MVC automaticky vytvoří `ObjectResult` pro vás objekt, který ho zalomí.</span><span class="sxs-lookup"><span data-stu-id="5c188-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="5c188-144">Klient získá formátovaný serializovaný objekt (výchozí formát JSON, můžete nakonfigurovat XML nebo jiné formáty).</span><span class="sxs-lookup"><span data-stu-id="5c188-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="5c188-145">Pokud je `null`vrácen objekt, pak rozhraní `204 No Content` vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="5c188-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="5c188-146">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="5c188-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="5c188-147">V ukázce bude požadavek na platný alias autora dostávat odpověď 200 OK s daty autora.</span><span class="sxs-lookup"><span data-stu-id="5c188-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="5c188-148">Požadavek na neplatný alias obdrží odpověď Content-204 bez obsahu.</span><span class="sxs-lookup"><span data-stu-id="5c188-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="5c188-149">Snímky obrazovky ukazující odpověď ve formátech XML a JSON jsou uvedené níže.</span><span class="sxs-lookup"><span data-stu-id="5c188-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="5c188-150">Proces vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="5c188-150">Content Negotiation Process</span></span>

<span data-ttu-id="5c188-151">K`Accept` *vyjednání* obsahu dochází pouze v případě, že se v požadavku objeví hlavička.</span><span class="sxs-lookup"><span data-stu-id="5c188-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="5c188-152">Když požadavek obsahuje hlavičku Accept, rozhraní provede výčet typů médií v hlavičce Accept v pořadí předvolby a pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z formátů určených hlavičkou Accept.</span><span class="sxs-lookup"><span data-stu-id="5c188-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="5c188-153">V případě, že se nenajde žádný formátovací modul, který může splnit požadavek klienta, rozhraní se pokusí najít první formátovací modul, který může vyvolat odpověď (pokud vývojář nenakonfiguroval možnost na `MvcOptions` vrácení 406. místo toho není přijatelné).</span><span class="sxs-lookup"><span data-stu-id="5c188-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="5c188-154">Pokud požadavek Určuje kód XML, ale formátovací modul XML nebyl nakonfigurován, bude použit formátovací modul JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="5c188-155">Obecně platí, že pokud není nakonfigurován žádný formátovací modul, který může poskytnout požadovaný formát, bude použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="5c188-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="5c188-156">Pokud není zadán žádný nadpis, bude při serializaci odpovědi použit první formátovací modul, který může zpracovat objekt, který má být vrácen.</span><span class="sxs-lookup"><span data-stu-id="5c188-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="5c188-157">V takovém případě se neuskuteční žádné vyjednávání – server určí, který formát bude používat.</span><span class="sxs-lookup"><span data-stu-id="5c188-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="5c188-158">Pokud hlavička Accept obsahuje `*/*`, záhlaví se bude ignorovat, pokud `RespectBrowserAcceptHeader` `MvcOptions`není nastavené na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="5c188-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="5c188-159">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="5c188-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="5c188-160">Na rozdíl od typických klientů rozhraní API mají webové prohlížeče `Accept` v úmyslu zadávat hlavičky, které obsahují nejrůznější formáty, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="5c188-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="5c188-161">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče, ignoruje `Accept` hlavičku a místo toho vrátí obsah v nakonfigurovaném výchozím formátu aplikace (JSON, pokud není nakonfigurované jinak).</span><span class="sxs-lookup"><span data-stu-id="5c188-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="5c188-162">To poskytuje jednotnější prostředí při použití různých prohlížečů ke využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5c188-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="5c188-163">Pokud upřednostňujete, `RespectBrowserAcceptHeader` aby aplikace přijímala hlavičky, můžete ji nakonfigurovat jako součást konfigurace MVC nastavením na `true` v `ConfigureServices` metodě v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5c188-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="5c188-164">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="5c188-164">Configuring Formatters</span></span>

<span data-ttu-id="5c188-165">Pokud vaše aplikace potřebuje podporovat další formáty nad rámec výchozího formátu JSON, můžete přidat balíčky NuGet a nakonfigurovat MVC na jejich podporu.</span><span class="sxs-lookup"><span data-stu-id="5c188-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="5c188-166">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="5c188-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="5c188-167">Vstupní formátovací moduly jsou používány [vazbou modelu](xref:mvc/models/model-binding); výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="5c188-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="5c188-168">Můžete také nakonfigurovat [vlastní formátovací](xref:web-api/advanced/custom-formatters)moduly.</span><span class="sxs-lookup"><span data-stu-id="5c188-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="5c188-169">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="5c188-169">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="5c188-170">Funkce pro `System.Text.Json`formátovací moduly založené na službě se dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="5c188-170">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="5c188-171">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="5c188-171">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="5c188-172">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5c188-172">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="5c188-173">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="5c188-173">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="5c188-174">Před ASP.NET Core 3,0 byla `Newtonsoft.Json` sada MVC použita jako výchozí pro použití formátovacích formátovacích formátu JSON implementovaných pomocí balíčku.</span><span class="sxs-lookup"><span data-stu-id="5c188-174">Prior to ASP.NET Core 3.0, MVC defaulted to using JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="5c188-175">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="5c188-175">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="5c188-176">Podpora formátovacích a funkcí založených na standardech je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a jeho konfiguraci v `Startup.ConfigureServices`. `Newtonsoft.Json`</span><span class="sxs-lookup"><span data-stu-id="5c188-176">Support for `Newtonsoft.Json`-based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddControllers()
    .AddNewtonsoftJson();
```

<span data-ttu-id="5c188-177">Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz `Newtonsoft.Json`na formátování založené na ASP.NET Core 3,0 vydání.</span><span class="sxs-lookup"><span data-stu-id="5c188-177">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters for the ASP.NET Core 3.0 release.</span></span> <span data-ttu-id="5c188-178">I nadále používat `Newtonsoft.Json`formátovací moduly založené na ASP.NET Core 3,0 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="5c188-178">Continue using the `Newtonsoft.Json`-based formatters if your ASP.NET Core 3.0 or later app:</span></span>

* <span data-ttu-id="5c188-179">Používá `Newtonsoft.Json` atributy ( `[JsonProperty]` například nebo `[JsonIgnore]`), přizpůsobuje nastavení serializace nebo spoléhá na funkce, které `Newtonsoft.Json` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="5c188-179">Uses `Newtonsoft.Json` attributes (for example, `[JsonProperty]` or `[JsonIgnore]`), customizes the serialization settings, or relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="5c188-180">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="5c188-180">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="5c188-181">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje `JsonSerializerSettings` instance, která je specifická pro `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="5c188-181">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="5c188-182">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="5c188-182">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="5c188-183">Funkce pro `Newtonsoft.Json`formátovací moduly založené na službě se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="5c188-183">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="5c188-184">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="5c188-184">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="5c188-185">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5c188-185">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

### <a name="add-xml-format-support"></a><span data-ttu-id="5c188-186">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="5c188-186">Add XML format support</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="5c188-187">Chcete-li přidat podporu formátování XML v ASP.NET Core 2,2 nebo starší, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací Modules. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="5c188-187">To add XML formatting support in ASP.NET Core 2.2 or earlier, install the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

::: moniker-end

<span data-ttu-id="5c188-188">Formátovací moduly XML implementované pomocí `System.Xml.Serialization.XmlSerializer` lze nakonfigurovat voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c188-188">XML formatters implemented using `System.Xml.Serialization.XmlSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="5c188-189">Formátovací moduly XML implementované pomocí `System.Runtime.Serialization.DataContractSerializer` lze také nakonfigurovat voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c188-189">Alternatively, XML formatters implemented using `System.Runtime.Serialization.DataContractSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

<span data-ttu-id="5c188-190">Jakmile přidáte podporu pro formátování XML, metody kontroleru by měly vracet odpovídající formát na základě `Accept` hlavičky žádosti, jak ukazuje tento příklad Fiddler:</span><span class="sxs-lookup"><span data-stu-id="5c188-190">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Konzola Fiddler: Nepůvodní karta pro požadavek zobrazuje hodnotu přijmout hlavičku je Application/XML.](formatting/_static/xml-response.png)

<span data-ttu-id="5c188-193">Na kartě kontrolori si můžete prohlédnout nezpracovaný požadavek GET s `Accept: application/xml` hlavičkovou sadou.</span><span class="sxs-lookup"><span data-stu-id="5c188-193">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="5c188-194">V podokně odpověď se zobrazí `Content-Type: application/xml` záhlaví `Author` a objekt byl serializován do formátu XML.</span><span class="sxs-lookup"><span data-stu-id="5c188-194">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="5c188-195">Pomocí karty skladatele upravte požadavek na zadání `application/json` `Accept` v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="5c188-195">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="5c188-196">Spusťte požadavek a odpověď bude formátována jako JSON:</span><span class="sxs-lookup"><span data-stu-id="5c188-196">Execute the request, and the response will be formatted as JSON:</span></span>

![Konzola Fiddler: Nepůvodní karta pro požadavek zobrazuje hodnotu přijmout hlavičku je Application/JSON.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="5c188-199">Na tomto snímku obrazovky vidíte, že požadavek nastaví hlavičku `Accept: application/json` a odpověď určuje stejnou hodnotu jako její. `Content-Type`</span><span class="sxs-lookup"><span data-stu-id="5c188-199">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="5c188-200">`Author` Objekt je zobrazen v těle odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-200">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="5c188-201">Vynucení konkrétního formátu</span><span class="sxs-lookup"><span data-stu-id="5c188-201">Forcing a Particular Format</span></span>

<span data-ttu-id="5c188-202">Pokud chcete omezit formáty odezvy pro konkrétní akci, můžete použít `[Produces]` filtr.</span><span class="sxs-lookup"><span data-stu-id="5c188-202">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="5c188-203">`[Produces]` Filtr určuje formáty odezvy konkrétní akce (nebo kontroleru).</span><span class="sxs-lookup"><span data-stu-id="5c188-203">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="5c188-204">Podobně jako u většiny [filtrů](xref:mvc/controllers/filters)můžete tuto možnost použít na akci, řadič nebo globální rozsah.</span><span class="sxs-lookup"><span data-stu-id="5c188-204">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="5c188-205">Filtr vynutí všechny akce `AuthorsController` v rámci, aby vracely odpovědi ve formátu JSON, `Accept` a to i v případě, že se pro aplikaci nakonfigurovaly jiné formátovací moduly a klient zadal hlavičku s požadavkem na jinou dostupnou. `[Produces]` formátovat.</span><span class="sxs-lookup"><span data-stu-id="5c188-205">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="5c188-206">Další informace, včetně toho, jak filtry použít globálně, najdete v tématu [filtry](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="5c188-206">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="5c188-207">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="5c188-207">Special Case Formatters</span></span>

<span data-ttu-id="5c188-208">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="5c188-208">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="5c188-209">Ve výchozím nastavení `string` budou návratové typy formátovány jako *Text/prostý* (*text/HTML* , pokud `Accept` jsou požadovány prostřednictvím záhlaví).</span><span class="sxs-lookup"><span data-stu-id="5c188-209">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="5c188-210">Toto chování je možné odebrat odebráním `TextOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="5c188-210">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="5c188-211">Odstraňte formátovací moduly v `Configure` metodě v *Startup.cs* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="5c188-211">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="5c188-212">Akce, které mají návratový typ objektu modelu, vrátí 204 bez odezvy obsahu `null`, pokud se vrátí.</span><span class="sxs-lookup"><span data-stu-id="5c188-212">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="5c188-213">Toto chování je možné odebrat odebráním `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="5c188-213">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="5c188-214">Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="5c188-214">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="5c188-215">`TextOutputFormatter` Bezhodnotvrátínávratové`string` typy 406, například.</span><span class="sxs-lookup"><span data-stu-id="5c188-215">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="5c188-216">Všimněte si, že pokud formátovací modul XML existuje, naformátuje `string` návratové typy, `TextOutputFormatter` Pokud je odebrán.</span><span class="sxs-lookup"><span data-stu-id="5c188-216">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="5c188-217">`HttpNoContentOutputFormatter`Bez objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="5c188-217">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="5c188-218">Například formátovací modul JSON jednoduše vrátí odpověď s tělem `null`, zatímco formátovací modul XML vrátí prázdný element XML s nastaveným atributem. `xsi:nil="true"`</span><span class="sxs-lookup"><span data-stu-id="5c188-218">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="5c188-219">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="5c188-219">Response Format URL Mappings</span></span>

<span data-ttu-id="5c188-220">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například v řetězci dotazu nebo části cesty, nebo pomocí přípony souboru specifického pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="5c188-220">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="5c188-221">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5c188-221">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="5c188-222">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5c188-222">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="5c188-223">Tato trasa umožní zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="5c188-223">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="5c188-224">Atribut kontroluje existenci hodnoty formátu `RouteData` v a při vytvoření odpovědi namapuje formát odpovědi na příslušný formátovací modul. `[FormatFilter]`</span><span class="sxs-lookup"><span data-stu-id="5c188-224">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="5c188-225">Cestě</span><span class="sxs-lookup"><span data-stu-id="5c188-225">Route</span></span>            |             <span data-ttu-id="5c188-226">Modul</span><span class="sxs-lookup"><span data-stu-id="5c188-226">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="5c188-227">Výchozí výstupní formátovací modul</span><span class="sxs-lookup"><span data-stu-id="5c188-227">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="5c188-228">Formátovací modul JSON (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="5c188-228">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="5c188-229">Formátovací modul XML (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="5c188-229">The XML formatter (if configured)</span></span>  |
