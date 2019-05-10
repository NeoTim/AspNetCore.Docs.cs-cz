---
title: Formátování dat odpovědi v rozhraní Web API ASP.NET Core
author: ardalis
description: Zjistěte, jak k formátování dat odpovědi v rozhraní Web API ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/14/2016
uid: web-api/advanced/formatting
ms.openlocfilehash: 04f5b3c544cf3fc47c8321c8233535400fcf55f4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902865"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="31f2f-103">Formátování dat odpovědi v rozhraní Web API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31f2f-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="31f2f-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="31f2f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="31f2f-105">ASP.NET Core MVC obsahuje integrovanou podporu pro formátování dat odpovědi pomocí pevné formáty nebo v reakci na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="31f2f-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="31f2f-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31f2f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="31f2f-107">Výsledky specifické pro formát akcí</span><span class="sxs-lookup"><span data-stu-id="31f2f-107">Format-Specific Action Results</span></span>

<span data-ttu-id="31f2f-108">Některé typy výsledků akcí, jako jsou specifické pro konkrétní formát `JsonResult` a `ContentResult`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="31f2f-109">Akce může vrátit konkrétní výsledky, které jsou vždy formátována určitým způsobem.</span><span class="sxs-lookup"><span data-stu-id="31f2f-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="31f2f-110">Například vrácení `JsonResult` vrátí data ve formátu JSON, bez ohledu na předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="31f2f-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="31f2f-111">Podobně, vrácení `ContentResult` vrátí data ve formátu prostého textu řetězce (stejně jako jednoduše vrátit řetězec).</span><span class="sxs-lookup"><span data-stu-id="31f2f-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="31f2f-112">Akce nemusí vracet žádné konkrétní typ; MVC podporuje libovolný objekt návratovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="31f2f-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="31f2f-113">Akce vrátí-li `IActionResult` provádění a kontroler dědí z `Controller`, je vývojářům k dispozici mnoho pomocné metody odpovídající na řadu možností.</span><span class="sxs-lookup"><span data-stu-id="31f2f-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="31f2f-114">Výsledky z akcí, které vracejí objekty, které nejsou `IActionResult` typy se serializovat pomocí odpovídající `IOutputFormatter` implementace.</span><span class="sxs-lookup"><span data-stu-id="31f2f-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="31f2f-115">Vrácení dat v určitém formátu z kontroler, který dědí z `Controller` základní třídy, použijte metodu integrovaných pomocných `Json` vrátit JSON a `Content` pro prostý text.</span><span class="sxs-lookup"><span data-stu-id="31f2f-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="31f2f-116">Vaše metoda akce by měla vrátit buď konkrétní výsledný typ (například `JsonResult`) nebo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="31f2f-117">Vrácení dat ve formátu JSON:</span><span class="sxs-lookup"><span data-stu-id="31f2f-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="31f2f-118">Ukázková odpověď z tuto akci:</span><span class="sxs-lookup"><span data-stu-id="31f2f-118">Sample response from this action:</span></span>

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazující typ obsahu odpovědi je application/json](formatting/_static/json-response.png)

<span data-ttu-id="31f2f-120">Všimněte si, že je typem obsahu odpovědi `application/json`, jak je znázorněno v seznamu síťových požadavků i v sekci hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31f2f-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="31f2f-121">Všimněte si také seznam možností v prohlížeči (v tomto případě Microsoft Edge) v hlavičce Accept v části záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="31f2f-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="31f2f-122">Současnou techniku ignoruje tuto hlavičku; obeying ho, jsou popsány níže.</span><span class="sxs-lookup"><span data-stu-id="31f2f-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="31f2f-123">Chcete-li vrátit data ve formátu prostého textu, použijte `ContentResult` a `Content` pomocné rutiny:</span><span class="sxs-lookup"><span data-stu-id="31f2f-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="31f2f-124">Odpověď z tuto akci:</span><span class="sxs-lookup"><span data-stu-id="31f2f-124">A response from this action:</span></span>

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazující typ obsahu odpovědi je text/plain](formatting/_static/text-response.png)

<span data-ttu-id="31f2f-126">Poznámka: v tomto případě `Content-Type` vrátil je `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="31f2f-127">Také můžete dosáhnout toto stejné chování pomocí pouze typ odpovědi řetězec:</span><span class="sxs-lookup"><span data-stu-id="31f2f-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="31f2f-128">U netriviální akcí s několika vrátí typy nebo možností (například jiné stavové kódy HTTP na základě výsledku operace prováděné), při odstraňování preferujte `IActionResult` jako návratový typ.</span><span class="sxs-lookup"><span data-stu-id="31f2f-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="31f2f-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="31f2f-129">Content Negotiation</span></span>

<span data-ttu-id="31f2f-130">Vyjednávání obsahu (*conneg* zkráceně) nastane, pokud klient Určuje [hlavičky Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="31f2f-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="31f2f-131">Výchozí formát používaný čtečkou ASP.NET Core MVC je JSON.</span><span class="sxs-lookup"><span data-stu-id="31f2f-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="31f2f-132">Vyjednávání obsahu je implementováno `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="31f2f-133">Je také součástí stavový kód určité akce výsledky vracené z metod helper (které jsou všechny založené na `ObjectResult`).</span><span class="sxs-lookup"><span data-stu-id="31f2f-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="31f2f-134">Můžete se taky vrátit typ modelu (třídy, které jste definovali jako typ přenosu dat) a rozhraní budou automaticky zabalte ji v `ObjectResult` za vás.</span><span class="sxs-lookup"><span data-stu-id="31f2f-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="31f2f-135">Používá následující metody akce `Ok` a `NotFound` pomocné metody:</span><span class="sxs-lookup"><span data-stu-id="31f2f-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="31f2f-136">Odpověď ve formátu JSON se vrátí, pokud byl požadován jiného formátu a server může vrátit požadovanému formátu.</span><span class="sxs-lookup"><span data-stu-id="31f2f-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="31f2f-137">Můžete použít nástroje, jako je [Fiddler](http://www.telerik.com/fiddler) vytvořte žádost, která obsahuje hlavičku Accept a určete jiný formát.</span><span class="sxs-lookup"><span data-stu-id="31f2f-137">You can use a tool like [Fiddler](http://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="31f2f-138">V takovém případě pokud má server *formátovací modul* , který může vytvořit odpověď požadovaný formát, výsledkem bude vrácen ve formátu upřednostňované klienta.</span><span class="sxs-lookup"><span data-stu-id="31f2f-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![Fiddler konzola znázorňující ručně vytvořili získat požadavek s hodnotou hlavičky Accept application/XML](formatting/_static/fiddler-composer.png)

<span data-ttu-id="31f2f-140">Ve výše uvedeném snímku obrazovky se použil autora aplikace Fiddler k vytvoření žádosti, určení `Accept: application/xml`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="31f2f-141">Ve výchozím nastavení, ASP.NET Core MVC podporuje pouze JSON, takže i když je určený jiný formát, vrácený výsledek je stále ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="31f2f-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="31f2f-142">Uvidíte jak přidat další formátování v další části.</span><span class="sxs-lookup"><span data-stu-id="31f2f-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="31f2f-143">Akce kontroleru vrátit POCOs (Plain původní objekty CLR), v takovém případě automaticky vytvoří ASP.NET Core MVC `ObjectResult` za vás, která obaluje objekt.</span><span class="sxs-lookup"><span data-stu-id="31f2f-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="31f2f-144">Klient získá formátovaný serializovaný objekt (formát JSON je výchozí, můžete nakonfigurovat, XML nebo jiných formátů).</span><span class="sxs-lookup"><span data-stu-id="31f2f-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="31f2f-145">Pokud je objekt vrácen je `null`, pak se vrátíte rozhraní framework `204 No Content` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31f2f-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="31f2f-146">Vrátí typ objektu:</span><span class="sxs-lookup"><span data-stu-id="31f2f-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="31f2f-147">Ve vzorku obdrží žádost o platné Autor alias odpověď 200 OK údaji autora.</span><span class="sxs-lookup"><span data-stu-id="31f2f-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="31f2f-148">Žádost o neplatný alias přijetí 204 bez obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31f2f-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="31f2f-149">Snímky obrazovky zobrazující odpověď ve formátu XML a JSON jsou uvedeny níže.</span><span class="sxs-lookup"><span data-stu-id="31f2f-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="31f2f-150">Zpracování vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="31f2f-150">Content Negotiation Process</span></span>

<span data-ttu-id="31f2f-151">Obsahu *vyjednávání* pouze probíhá-li `Accept` hlavička objeví v požadavku.</span><span class="sxs-lookup"><span data-stu-id="31f2f-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="31f2f-152">Pokud požadavek obsahuje hlavičku accept, rozhraní se zobrazí výčet typů médií v hlavičce accept v pořadí priority a pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z formátů zadaná v hlavičce accept.</span><span class="sxs-lookup"><span data-stu-id="31f2f-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="31f2f-153">V případě, že není formátování nalezeno, která splňují požadavek klienta, rozhraní se pokusí najít první formátovací modul, který může vytvořit odpověď (Pokud pro vývojáře se nakonfigurovala možnost v `MvcOptions` vrátit 406 Nepřijatelný místo).</span><span class="sxs-lookup"><span data-stu-id="31f2f-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="31f2f-154">Pokud požadavek určuje XML, ale formátovací modul XML není nakonfigurovaná, budou formátovací modul JSON použít.</span><span class="sxs-lookup"><span data-stu-id="31f2f-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="31f2f-155">Obecně platí Pokud je nakonfigurovaná žádná formátovací modul, který může poskytnout požadovaný formát, pak první formátovací modul, která dokáže formátovat objektu se používá.</span><span class="sxs-lookup"><span data-stu-id="31f2f-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="31f2f-156">Pokud není uveden žádný záhlaví, první formátovací modul, který dokáže zpracovat objekt, který má být vrácen se použije k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31f2f-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="31f2f-157">V takovém případě není k dispozici žádné vyjednávání dochází – na serveru je určení jakém formátu bude používat.</span><span class="sxs-lookup"><span data-stu-id="31f2f-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="31f2f-158">Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` je nastavena na hodnotu true v `MvcOptions`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="31f2f-159">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="31f2f-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="31f2f-160">Na rozdíl od typických klientů rozhraní API, webové prohlížeče jsou obvykle slouží k poskytování `Accept` hlavičky, které obsahují širokou škálu formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="31f2f-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="31f2f-161">Ve výchozím nastavení, když rozhraní zjistí, že žádost pochází z prohlížeče, se bude ignorovat `Accept` hlavičky a místo toho návratový obsah v aplikaci nakonfigurovaná výchozí formát (JSON není-li jinak nakonfigurované).</span><span class="sxs-lookup"><span data-stu-id="31f2f-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="31f2f-162">To poskytuje konzistentní prostředí při použití různých prohlížečů k používání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="31f2f-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="31f2f-163">Pokud si přejete prohlížeče dodržet aplikace hlavičky accept, to můžete nakonfigurovat jako součást konfigurace MVC nastavením `RespectBrowserAcceptHeader` k `true` v `ConfigureServices` metoda *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="31f2f-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="31f2f-164">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="31f2f-164">Configuring Formatters</span></span>

<span data-ttu-id="31f2f-165">Pokud vaše aplikace potřebuje pro podporu dalších formátech nad výchozí hodnotu JSON, můžete přidat balíčky NuGet a nakonfigurovat MVC pro jejich podporu.</span><span class="sxs-lookup"><span data-stu-id="31f2f-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="31f2f-166">Existují samostatné formátovací moduly pro vstup a výstup.</span><span class="sxs-lookup"><span data-stu-id="31f2f-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="31f2f-167">Vstupní formátovací moduly jsou používány [vazby modelu](xref:mvc/models/model-binding); formátování výstupu se používají k formátování odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31f2f-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="31f2f-168">Můžete taky nakonfigurovat [vlastní formátování](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="31f2f-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

### <a name="adding-xml-format-support"></a><span data-ttu-id="31f2f-169">Přidání podpory pro formát XML</span><span class="sxs-lookup"><span data-stu-id="31f2f-169">Adding XML Format Support</span></span>

<span data-ttu-id="31f2f-170">Chcete-li přidat podporu pro formát XML, nainstalujte `Microsoft.AspNetCore.Mvc.Formatters.Xml` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="31f2f-170">To add support for XML formatting, install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

<span data-ttu-id="31f2f-171">Přidat XmlSerializerFormatters do konfigurace MVC v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="31f2f-171">Add the XmlSerializerFormatters to MVC's configuration in *Startup.cs*:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="31f2f-172">Alternativně můžete přidat jenom formátování výstupu:</span><span class="sxs-lookup"><span data-stu-id="31f2f-172">Alternately, you can add just the output formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

<span data-ttu-id="31f2f-173">Tyto dvě metody se serializace výsledků pomocí `System.Xml.Serialization.XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-173">These two approaches will serialize results using `System.Xml.Serialization.XmlSerializer`.</span></span> <span data-ttu-id="31f2f-174">Pokud dáváte přednost, můžete použít `System.Runtime.Serialization.DataContractSerializer` tak, že přidáte jeho přidružené formátovací modul:</span><span class="sxs-lookup"><span data-stu-id="31f2f-174">If you prefer, you can use the `System.Runtime.Serialization.DataContractSerializer` by adding its associated formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

<span data-ttu-id="31f2f-175">Po přidání podpory pro formát XML, by měl vrátit svoje metody kontroleru vhodný formát na základě daného požadavku `Accept` záhlaví jako Fiddler, tento příklad ukazuje:</span><span class="sxs-lookup"><span data-stu-id="31f2f-175">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler konzoly: Nezpracovaná karta pro požadavek zobrazuje, že je hodnota hlavičky Accept application/xml.](formatting/_static/xml-response.png)

<span data-ttu-id="31f2f-178">Se zobrazí v kartě kontroly, která byla vytvořena žádost o získání nezpracovaných se `Accept: application/xml` hlavička set.</span><span class="sxs-lookup"><span data-stu-id="31f2f-178">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="31f2f-179">Zobrazí se podokno odpovědi `Content-Type: application/xml` záhlaví a `Author` objekt má byl serializován do formátu XML.</span><span class="sxs-lookup"><span data-stu-id="31f2f-179">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="31f2f-180">Použijte kartu Composer upravit požadavku k určení `application/json` v `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="31f2f-180">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="31f2f-181">Provedení požadavku a odpovědi naformátovaný jako JSON:</span><span class="sxs-lookup"><span data-stu-id="31f2f-181">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler konzoly: Nezpracovaná karta pro požadavek zobrazuje, že je hodnota hlavičky Accept application/json.](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="31f2f-184">Na tomto snímku obrazovky vidíte požadavek nastaví hlavičku z `Accept: application/json` a odpovědi určuje stejný jako jeho `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-184">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="31f2f-185">`Author` Objektu se zobrazí v těle odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="31f2f-185">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="31f2f-186">Vynucení konkrétní formát</span><span class="sxs-lookup"><span data-stu-id="31f2f-186">Forcing a Particular Format</span></span>

<span data-ttu-id="31f2f-187">Pokud chcete omezit formáty odpovědi pro konkrétní akci je možné, můžete použít `[Produces]` filtru.</span><span class="sxs-lookup"><span data-stu-id="31f2f-187">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="31f2f-188">`[Produces]` Filtr určuje formát odpovědi pro konkrétní akci (nebo řadič).</span><span class="sxs-lookup"><span data-stu-id="31f2f-188">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="31f2f-189">Jako většina [filtry](xref:mvc/controllers/filters), to je možné použít na akci, kontroler nebo globální obor.</span><span class="sxs-lookup"><span data-stu-id="31f2f-189">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="31f2f-190">`[Produces]` Filtr vynutí všechny akce v rámci `AuthorsController` k vrácení odpovědí ve formátu JSON i v případě jiných formátovací moduly byly nakonfigurované pro aplikaci a poskytnutý klientem `Accept` záhlaví žádosti o jiné, k dispozici formát.</span><span class="sxs-lookup"><span data-stu-id="31f2f-190">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="31f2f-191">Zobrazit [filtry](xref:mvc/controllers/filters) Další informace, včetně postupu jak používat filtry globálně.</span><span class="sxs-lookup"><span data-stu-id="31f2f-191">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="31f2f-192">Zvláštní případ formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="31f2f-192">Special Case Formatters</span></span>

<span data-ttu-id="31f2f-193">Některé speciální případy jsou implementovány pomocí předdefinované formátování.</span><span class="sxs-lookup"><span data-stu-id="31f2f-193">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="31f2f-194">Ve výchozím nastavení `string` návratové typy naformátovaný jako *text/plain* (*text/html* žádost prostřednictvím `Accept` záhlaví).</span><span class="sxs-lookup"><span data-stu-id="31f2f-194">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="31f2f-195">Toto chování lze odebrat odstraněním `TextOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-195">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="31f2f-196">Odebrat formátování v `Configure` metoda *Startup.cs* (viz dole).</span><span class="sxs-lookup"><span data-stu-id="31f2f-196">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="31f2f-197">Akce, které mají objekt modelu vrátit typ vrátí 204 neodpovídá obsahu při vrácení `null`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-197">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="31f2f-198">Toto chování lze odebrat odstraněním `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-198">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="31f2f-199">Následující kód, odebere `TextOutputFormatter` a `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="31f2f-199">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="31f2f-200">Bez `TextOutputFormatter`, `string` vrátí typy vrátit 406 Nepřijatelný, například.</span><span class="sxs-lookup"><span data-stu-id="31f2f-200">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="31f2f-201">Všimněte si, že pokud existuje formátovací modul XML ji naformátuje `string` návratové typy, pokud `TextOutputFormatter` se odebere.</span><span class="sxs-lookup"><span data-stu-id="31f2f-201">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="31f2f-202">Bez `HttpNoContentOutputFormatter`, objektů null jsou formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="31f2f-202">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="31f2f-203">Například formátování JSON jednoduše vrátit odpověď se na znalostech `null`, zatímco formátovací modul XML vrátí prázdný element XML s atributem `xsi:nil="true"` nastavit.</span><span class="sxs-lookup"><span data-stu-id="31f2f-203">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="31f2f-204">Mapování formát adresy URL odpovědi</span><span class="sxs-lookup"><span data-stu-id="31f2f-204">Response Format URL Mappings</span></span>

<span data-ttu-id="31f2f-205">Klienti mohou požadovat konkrétní formát jako část adresy URL, například v řetězci dotazu nebo části cesty nebo pomocí rozšíření specifické pro formát souborů například XML nebo .json.</span><span class="sxs-lookup"><span data-stu-id="31f2f-205">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="31f2f-206">Mapování z cestu požadavku by měl určený v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="31f2f-206">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="31f2f-207">Příklad:</span><span class="sxs-lookup"><span data-stu-id="31f2f-207">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="31f2f-208">Tato trasa by umožnilo požadovaný formát zadaný jako volitelný soubor rozšíření.</span><span class="sxs-lookup"><span data-stu-id="31f2f-208">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="31f2f-209">`[FormatFilter]` Atribut zkontroluje existenci hodnoty formátu `RouteData` a bude mapovat formát odpovědi na odpovídající formátovací modul, když je vytvořen odpověď.</span><span class="sxs-lookup"><span data-stu-id="31f2f-209">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="31f2f-210">trasy</span><span class="sxs-lookup"><span data-stu-id="31f2f-210">Route</span></span>            |             <span data-ttu-id="31f2f-211">Formátovací modul</span><span class="sxs-lookup"><span data-stu-id="31f2f-211">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="31f2f-212">Výchozí formátování výstupu</span><span class="sxs-lookup"><span data-stu-id="31f2f-212">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="31f2f-213">Formátování JSON (je-li konfigurováno)</span><span class="sxs-lookup"><span data-stu-id="31f2f-213">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="31f2f-214">Formátovací modul XML (Pokud je nakonfigurovaná)</span><span class="sxs-lookup"><span data-stu-id="31f2f-214">The XML formatter (if configured)</span></span>  |
