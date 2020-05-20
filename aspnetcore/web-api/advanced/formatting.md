---
<span data-ttu-id="511e3-101">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-102">'Blazor'</span></span>
- <span data-ttu-id="511e3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-103">'Identity'</span></span>
- <span data-ttu-id="511e3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-105">'Razor'</span></span>
- <span data-ttu-id="511e3-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="511e3-107">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="511e3-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="511e3-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="511e3-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="511e3-109">ASP.NET Core MVC podporuje formátování dat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="511e3-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="511e3-110">Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.</span><span class="sxs-lookup"><span data-stu-id="511e3-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="511e3-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="511e3-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="511e3-112">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="511e3-112">Format-specific Action Results</span></span>

<span data-ttu-id="511e3-113">Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="511e3-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="511e3-114">Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="511e3-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="511e3-115">Například vrácení `JsonResult` vrátí data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="511e3-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="511e3-116">Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="511e3-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="511e3-117">Akce není nutná k vrácení určitého konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="511e3-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="511e3-118">ASP.NET Core podporuje návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="511e3-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="511e3-119">Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult> typy, jsou serializovány pomocí příslušné <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementace.</span><span class="sxs-lookup"><span data-stu-id="511e3-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="511e3-120">Další informace naleznete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="511e3-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="511e3-121">Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="511e3-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="511e3-122">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="511e3-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="511e3-123">Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="511e3-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="511e3-124">Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="511e3-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="511e3-125">Zobrazí se hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="511e3-125">The request headers are displayed.</span></span> <span data-ttu-id="511e3-126">Například `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="511e3-126">For example, the `Accept` header.</span></span> <span data-ttu-id="511e3-127">`Accept`Hlavička je ignorována v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="511e3-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="511e3-128">Chcete-li vrátit data ve formátu prostého textu, použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocníka:</span><span class="sxs-lookup"><span data-stu-id="511e3-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="511e3-129">V předchozím kódu `Content-Type` je vráceno `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="511e3-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="511e3-130">Vrácení řetězcového doručení `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="511e3-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="511e3-131">Pro akce s více návratovými typy vrátí `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="511e3-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="511e3-132">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="511e3-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="511e3-133">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="511e3-133">Content negotiation</span></span>

<span data-ttu-id="511e3-134">K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="511e3-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="511e3-135">Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="511e3-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="511e3-136">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="511e3-136">Content negotiation is:</span></span>

* <span data-ttu-id="511e3-137">Implementuje <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="511e3-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="511e3-138">Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="511e3-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="511e3-139">Pomocné metody výsledků akcí jsou založeny na `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="511e3-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="511e3-140">Při vrácení typu modelu je návratový typ `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="511e3-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="511e3-141">Následující metoda akce používá `Ok` `NotFound` pomocné metody a:</span><span class="sxs-lookup"><span data-stu-id="511e3-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="511e3-142">Ve výchozím nastavení ASP.NET Core podporuje `application/json` , `text/json` a `text/plain` typy médií.</span><span class="sxs-lookup"><span data-stu-id="511e3-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="511e3-143">Nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , mohou nastavit `Accept` hlavičku požadavku na zadání návratového formátu.</span><span class="sxs-lookup"><span data-stu-id="511e3-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="511e3-144">Pokud `Accept` Hlavička obsahuje typ, který server podporuje, vrátí se tento typ.</span><span class="sxs-lookup"><span data-stu-id="511e3-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="511e3-145">V další části se dozvíte, jak přidat další formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="511e3-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="511e3-146">Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu).</span><span class="sxs-lookup"><span data-stu-id="511e3-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="511e3-147">Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult` objekt, který zabalí objekt.</span><span class="sxs-lookup"><span data-stu-id="511e3-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="511e3-148">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="511e3-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="511e3-149">Pokud je vrácen objekt `null` , `204 No Content` je vrácena odpověď.</span><span class="sxs-lookup"><span data-stu-id="511e3-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="511e3-150">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="511e3-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="511e3-151">V předchozím kódu vrátí požadavek na platný alias autora `200 OK` odpověď s daty autora.</span><span class="sxs-lookup"><span data-stu-id="511e3-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="511e3-152">Požadavek na neplatný alias vrací `204 No Content` odpověď.</span><span class="sxs-lookup"><span data-stu-id="511e3-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="511e3-153">Hlavička Accept</span><span class="sxs-lookup"><span data-stu-id="511e3-153">The Accept header</span></span>

<span data-ttu-id="511e3-154">Po zobrazení hlavičky v požadavku se *domlouvání* obsahu provede `Accept` .</span><span class="sxs-lookup"><span data-stu-id="511e3-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="511e3-155">Když požadavek obsahuje hlavičku Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="511e3-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="511e3-156">Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="511e3-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="511e3-157">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.</span><span class="sxs-lookup"><span data-stu-id="511e3-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="511e3-158">Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="511e3-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="511e3-159">Vrátí `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> , pokud byla nastavena, nebo-</span><span class="sxs-lookup"><span data-stu-id="511e3-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="511e3-160">Pokusí se najít první formátovací modul, který může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="511e3-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="511e3-161">Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="511e3-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="511e3-162">Pokud `Accept` se v požadavku nezobrazí žádná hlavička:</span><span class="sxs-lookup"><span data-stu-id="511e3-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="511e3-163">První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="511e3-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="511e3-164">Nedochází k žádnému vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="511e3-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="511e3-165">Server určí, který formát se má vrátit.</span><span class="sxs-lookup"><span data-stu-id="511e3-165">The server is determining what format to return.</span></span>

<span data-ttu-id="511e3-166">Pokud hlavička Accept obsahuje `*/*` , záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není nastavené na hodnotu true <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="511e3-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="511e3-167">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="511e3-167">Browsers and content negotiation</span></span>

<span data-ttu-id="511e3-168">Na rozdíl od typických klientů rozhraní API webové prohlížeče poskytují `Accept` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="511e3-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="511e3-169">Webový prohlížeč určí mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="511e3-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="511e3-170">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="511e3-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="511e3-171">`Accept`Záhlaví je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="511e3-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="511e3-172">Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="511e3-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="511e3-173">Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="511e3-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="511e3-174">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí prohlížeče, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :</span><span class="sxs-lookup"><span data-stu-id="511e3-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="511e3-175">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="511e3-175">Configure formatters</span></span>

<span data-ttu-id="511e3-176">Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="511e3-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="511e3-177">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="511e3-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="511e3-178">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="511e3-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="511e3-179">Výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="511e3-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="511e3-180">Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="511e3-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="511e3-181">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="511e3-181">Add XML format support</span></span>

<span data-ttu-id="511e3-182">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="511e3-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="511e3-183">Předchozí kód serializace výsledků pomocí `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="511e3-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="511e3-184">Při použití předchozího kódu vrátí metody kontroleru odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="511e3-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="511e3-185">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="511e3-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="511e3-186">Funkce pro formátovací moduly založené na službě se `System.Text.Json` dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="511e3-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="511e3-187">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="511e3-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="511e3-188">Například:</span><span class="sxs-lookup"><span data-stu-id="511e3-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="511e3-189">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="511e3-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="511e3-190">Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku.</span><span class="sxs-lookup"><span data-stu-id="511e3-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="511e3-191">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="511e3-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="511e3-192">Podpora `Newtonsoft.Json` formátování a funkcí založených na základech je k dispozici po instalaci [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíčku NuGet a jeho konfiguraci v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="511e3-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="511e3-193">Některé funkce nemusí fungovat správně s `System.Text.Json` formátovacími moduly a vyžadují odkaz na `Newtonsoft.Json` formátování na základě.</span><span class="sxs-lookup"><span data-stu-id="511e3-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="511e3-194">I nadále používat formátovací moduly založené na tom, `Newtonsoft.Json` Pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="511e3-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="511e3-195">Používá `Newtonsoft.Json` atributy.</span><span class="sxs-lookup"><span data-stu-id="511e3-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="511e3-196">Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="511e3-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="511e3-197">Přizpůsobuje nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="511e3-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="511e3-198">Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="511e3-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="511e3-199">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="511e3-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="511e3-200">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje instance `JsonSerializerSettings` , která je specifická pro `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="511e3-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="511e3-201">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="511e3-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="511e3-202">Funkce pro formátovací moduly založené na službě se `Newtonsoft.Json` dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="511e3-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="511e3-203">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="511e3-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="511e3-204">Například:</span><span class="sxs-lookup"><span data-stu-id="511e3-204">For example:</span></span>

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

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="511e3-205">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="511e3-205">Add XML format support</span></span>

<span data-ttu-id="511e3-206">Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="511e3-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="511e3-207">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="511e3-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="511e3-208">Předchozí kód serializace výsledků pomocí `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="511e3-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="511e3-209">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="511e3-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="511e3-210">Zadat formát</span><span class="sxs-lookup"><span data-stu-id="511e3-210">Specify a format</span></span>

<span data-ttu-id="511e3-211">Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr.</span><span class="sxs-lookup"><span data-stu-id="511e3-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="511e3-212">Podobně jako u většiny [filtrů](xref:mvc/controllers/filters) `[Produces]` lze použít akci, řadič nebo globální rozsah:</span><span class="sxs-lookup"><span data-stu-id="511e3-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="511e3-213">Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:</span><span class="sxs-lookup"><span data-stu-id="511e3-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="511e3-214">Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="511e3-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="511e3-215">Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.</span><span class="sxs-lookup"><span data-stu-id="511e3-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="511e3-216">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="511e3-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="511e3-217">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="511e3-217">Special case formatters</span></span>

<span data-ttu-id="511e3-218">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="511e3-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="511e3-219">Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud jsou požadovány prostřednictvím `Accept` hlavičky).</span><span class="sxs-lookup"><span data-stu-id="511e3-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="511e3-220">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="511e3-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="511e3-221">Formátovací moduly jsou v metodě odebrány `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="511e3-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="511e3-222">Akce, které mají vrácen návratový typ objektu modelu `204 No Content` při návratu `null` .</span><span class="sxs-lookup"><span data-stu-id="511e3-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="511e3-223">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="511e3-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="511e3-224">Následující kód odstraní `StringOutputFormatter` a `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="511e3-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="511e3-225">Bez rozhraní `StringOutputFormatter` , předdefinované formáty formátování JSON `string` vrátí typy.</span><span class="sxs-lookup"><span data-stu-id="511e3-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="511e3-226">Pokud je vestavěný formátovací modul JSON odebraný a je k dispozici formátovací modul XML, vrátí typy formátování formátovací kód XML `string` .</span><span class="sxs-lookup"><span data-stu-id="511e3-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="511e3-227">V opačném případě `string` návratové typy vrátí `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="511e3-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="511e3-228">Bez `HttpNoContentOutputFormatter` objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="511e3-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="511e3-229">Například:</span><span class="sxs-lookup"><span data-stu-id="511e3-229">For example:</span></span>

* <span data-ttu-id="511e3-230">Formátovací modul JSON vrátí odpověď s tělem `null` .</span><span class="sxs-lookup"><span data-stu-id="511e3-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="511e3-231">Formátovací modul XML vrátí prázdný element XML s `xsi:nil="true"` nastaveným atributem.</span><span class="sxs-lookup"><span data-stu-id="511e3-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="511e3-232">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="511e3-232">Response format URL mappings</span></span>

<span data-ttu-id="511e3-233">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="511e3-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="511e3-234">V řetězci dotazu nebo v části cesty.</span><span class="sxs-lookup"><span data-stu-id="511e3-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="511e3-235">Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="511e3-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="511e3-236">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="511e3-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="511e3-237">Například:</span><span class="sxs-lookup"><span data-stu-id="511e3-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="511e3-238">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="511e3-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="511e3-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atribut kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="511e3-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="511e3-240">Trasa</span><span class="sxs-lookup"><span data-stu-id="511e3-240">Route</span></span>        |             <span data-ttu-id="511e3-241">Modul</span><span class="sxs-lookup"><span data-stu-id="511e3-241">Formatter</span></span>              |
|---
<span data-ttu-id="511e3-242">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-243">'Blazor'</span></span>
- <span data-ttu-id="511e3-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-244">'Identity'</span></span>
- <span data-ttu-id="511e3-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-246">'Razor'</span></span>
- <span data-ttu-id="511e3-247">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-248">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-249">'Blazor'</span></span>
- <span data-ttu-id="511e3-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-250">'Identity'</span></span>
- <span data-ttu-id="511e3-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-252">'Razor'</span></span>
- <span data-ttu-id="511e3-253">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-254">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-255">'Blazor'</span></span>
- <span data-ttu-id="511e3-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-256">'Identity'</span></span>
- <span data-ttu-id="511e3-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-258">'Razor'</span></span>
- <span data-ttu-id="511e3-259">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-260">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-261">'Blazor'</span></span>
- <span data-ttu-id="511e3-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-262">'Identity'</span></span>
- <span data-ttu-id="511e3-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-264">'Razor'</span></span>
- <span data-ttu-id="511e3-265">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-266">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-267">'Blazor'</span></span>
- <span data-ttu-id="511e3-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-268">'Identity'</span></span>
- <span data-ttu-id="511e3-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-270">'Razor'</span></span>
- <span data-ttu-id="511e3-271">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-272">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-273">'Blazor'</span></span>
- <span data-ttu-id="511e3-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-274">'Identity'</span></span>
- <span data-ttu-id="511e3-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-276">'Razor'</span></span>
- <span data-ttu-id="511e3-277">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-278">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-279">'Blazor'</span></span>
- <span data-ttu-id="511e3-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-280">'Identity'</span></span>
- <span data-ttu-id="511e3-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-282">'Razor'</span></span>
- <span data-ttu-id="511e3-283">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-284">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-285">'Blazor'</span></span>
- <span data-ttu-id="511e3-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-286">'Identity'</span></span>
- <span data-ttu-id="511e3-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-288">'Razor'</span></span>
- <span data-ttu-id="511e3-289">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-290">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-291">'Blazor'</span></span>
- <span data-ttu-id="511e3-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-292">'Identity'</span></span>
- <span data-ttu-id="511e3-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-294">'Razor'</span></span>
- <span data-ttu-id="511e3-295">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-296">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-297">'Blazor'</span></span>
- <span data-ttu-id="511e3-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-298">'Identity'</span></span>
- <span data-ttu-id="511e3-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-300">'Razor'</span></span>
- <span data-ttu-id="511e3-301">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-301">'SignalR' uid:</span></span> 

<span data-ttu-id="511e3-302">------------| Název---: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-303">'Blazor'</span></span>
- <span data-ttu-id="511e3-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-304">'Identity'</span></span>
- <span data-ttu-id="511e3-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-306">'Razor'</span></span>
- <span data-ttu-id="511e3-307">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-308">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-309">'Blazor'</span></span>
- <span data-ttu-id="511e3-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-310">'Identity'</span></span>
- <span data-ttu-id="511e3-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-312">'Razor'</span></span>
- <span data-ttu-id="511e3-313">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-314">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-315">'Blazor'</span></span>
- <span data-ttu-id="511e3-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-316">'Identity'</span></span>
- <span data-ttu-id="511e3-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-318">'Razor'</span></span>
- <span data-ttu-id="511e3-319">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-320">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-321">'Blazor'</span></span>
- <span data-ttu-id="511e3-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-322">'Identity'</span></span>
- <span data-ttu-id="511e3-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-324">'Razor'</span></span>
- <span data-ttu-id="511e3-325">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-326">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-327">'Blazor'</span></span>
- <span data-ttu-id="511e3-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-328">'Identity'</span></span>
- <span data-ttu-id="511e3-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-330">'Razor'</span></span>
- <span data-ttu-id="511e3-331">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-332">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-333">'Blazor'</span></span>
- <span data-ttu-id="511e3-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-334">'Identity'</span></span>
- <span data-ttu-id="511e3-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-336">'Razor'</span></span>
- <span data-ttu-id="511e3-337">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-338">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-339">'Blazor'</span></span>
- <span data-ttu-id="511e3-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-340">'Identity'</span></span>
- <span data-ttu-id="511e3-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-342">'Razor'</span></span>
- <span data-ttu-id="511e3-343">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-344">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-345">'Blazor'</span></span>
- <span data-ttu-id="511e3-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-346">'Identity'</span></span>
- <span data-ttu-id="511e3-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-348">'Razor'</span></span>
- <span data-ttu-id="511e3-349">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-350">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-351">'Blazor'</span></span>
- <span data-ttu-id="511e3-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-352">'Identity'</span></span>
- <span data-ttu-id="511e3-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-354">'Razor'</span></span>
- <span data-ttu-id="511e3-355">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-356">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-357">'Blazor'</span></span>
- <span data-ttu-id="511e3-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-358">'Identity'</span></span>
- <span data-ttu-id="511e3-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-360">'Razor'</span></span>
- <span data-ttu-id="511e3-361">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-362">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-363">'Blazor'</span></span>
- <span data-ttu-id="511e3-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-364">'Identity'</span></span>
- <span data-ttu-id="511e3-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-366">'Razor'</span></span>
- <span data-ttu-id="511e3-367">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-368">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-369">'Blazor'</span></span>
- <span data-ttu-id="511e3-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-370">'Identity'</span></span>
- <span data-ttu-id="511e3-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-372">'Razor'</span></span>
- <span data-ttu-id="511e3-373">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-374">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-375">'Blazor'</span></span>
- <span data-ttu-id="511e3-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-376">'Identity'</span></span>
- <span data-ttu-id="511e3-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-378">'Razor'</span></span>
- <span data-ttu-id="511e3-379">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-380">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-381">'Blazor'</span></span>
- <span data-ttu-id="511e3-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-382">'Identity'</span></span>
- <span data-ttu-id="511e3-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-384">'Razor'</span></span>
- <span data-ttu-id="511e3-385">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-386">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-387">'Blazor'</span></span>
- <span data-ttu-id="511e3-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-388">'Identity'</span></span>
- <span data-ttu-id="511e3-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-390">'Razor'</span></span>
- <span data-ttu-id="511e3-391">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="511e3-392">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="511e3-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="511e3-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="511e3-393">'Blazor'</span></span>
- <span data-ttu-id="511e3-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="511e3-394">'Identity'</span></span>
- <span data-ttu-id="511e3-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="511e3-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="511e3-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="511e3-396">'Razor'</span></span>
- <span data-ttu-id="511e3-397">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="511e3-397">'SignalR' uid:</span></span> 

<span data-ttu-id="511e3-398">------------------| |   `/api/products/5`    |    Výchozí formátování výstupu | | `/api/products/5.json` | Formátovací modul JSON (Pokud je nakonfigurovaný) | | `/api/products/5.xml`  | Formátovací modul XML (Pokud je nakonfigurován) |</span><span class="sxs-lookup"><span data-stu-id="511e3-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
