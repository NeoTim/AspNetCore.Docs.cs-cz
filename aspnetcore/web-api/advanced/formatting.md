---
<span data-ttu-id="763e8-101">title: formátovat data odpovědi v ASP.NET Core Web API Author: ardalis Popis: Naučte se formátovat data odpovědí v ASP.NET Core webovém rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="763e8-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="763e8-102">MS. Author: Riande MS. Custom: H1Hack27Feb2017 MS. Date: 04/17/2020 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="763e8-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="763e8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="763e8-103">'Blazor'</span></span>
- <span data-ttu-id="763e8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="763e8-104">'Identity'</span></span>
- <span data-ttu-id="763e8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="763e8-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="763e8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="763e8-106">'Razor'</span></span>
- <span data-ttu-id="763e8-107">SignalRUID: Web-API/Rozšířené/formátování</span><span class="sxs-lookup"><span data-stu-id="763e8-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="763e8-108">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="763e8-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="763e8-109">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="763e8-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="763e8-110">ASP.NET Core MVC podporuje formátování dat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="763e8-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="763e8-111">Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.</span><span class="sxs-lookup"><span data-stu-id="763e8-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="763e8-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="763e8-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="763e8-113">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="763e8-113">Format-specific Action Results</span></span>

<span data-ttu-id="763e8-114">Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="763e8-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="763e8-115">Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="763e8-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="763e8-116">Například vrácení `JsonResult` vrátí data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="763e8-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="763e8-117">Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="763e8-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="763e8-118">Akce není nutná k vrácení určitého konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="763e8-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="763e8-119">ASP.NET Core podporuje návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="763e8-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="763e8-120">Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult> typy, jsou serializovány pomocí příslušné <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementace.</span><span class="sxs-lookup"><span data-stu-id="763e8-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="763e8-121">Další informace naleznete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="763e8-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="763e8-122">Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="763e8-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="763e8-123">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="763e8-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="763e8-124">Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="763e8-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="763e8-125">Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="763e8-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="763e8-126">Zobrazí se hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="763e8-126">The request headers are displayed.</span></span> <span data-ttu-id="763e8-127">Například `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="763e8-127">For example, the `Accept` header.</span></span> <span data-ttu-id="763e8-128">`Accept`Hlavička je ignorována v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="763e8-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="763e8-129">Chcete-li vrátit data ve formátu prostého textu, použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocníka:</span><span class="sxs-lookup"><span data-stu-id="763e8-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="763e8-130">V předchozím kódu `Content-Type` je vráceno `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="763e8-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="763e8-131">Vrácení řetězcového doručení `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="763e8-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="763e8-132">Pro akce s více návratovými typy vrátí `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="763e8-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="763e8-133">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="763e8-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="763e8-134">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="763e8-134">Content negotiation</span></span>

<span data-ttu-id="763e8-135">K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="763e8-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="763e8-136">Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="763e8-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="763e8-137">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="763e8-137">Content negotiation is:</span></span>

* <span data-ttu-id="763e8-138">Implementuje <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="763e8-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="763e8-139">Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="763e8-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="763e8-140">Pomocné metody výsledků akcí jsou založeny na `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="763e8-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="763e8-141">Při vrácení typu modelu je návratový typ `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="763e8-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="763e8-142">Následující metoda akce používá `Ok` `NotFound` pomocné metody a:</span><span class="sxs-lookup"><span data-stu-id="763e8-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="763e8-143">Ve výchozím nastavení ASP.NET Core podporuje `application/json` , `text/json` a `text/plain` typy médií.</span><span class="sxs-lookup"><span data-stu-id="763e8-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="763e8-144">Nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , mohou nastavit `Accept` hlavičku požadavku na zadání návratového formátu.</span><span class="sxs-lookup"><span data-stu-id="763e8-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="763e8-145">Pokud `Accept` Hlavička obsahuje typ, který server podporuje, vrátí se tento typ.</span><span class="sxs-lookup"><span data-stu-id="763e8-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="763e8-146">V další části se dozvíte, jak přidat další formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="763e8-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="763e8-147">Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu).</span><span class="sxs-lookup"><span data-stu-id="763e8-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="763e8-148">Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult` objekt, který zabalí objekt.</span><span class="sxs-lookup"><span data-stu-id="763e8-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="763e8-149">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="763e8-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="763e8-150">Pokud je vrácen objekt `null` , `204 No Content` je vrácena odpověď.</span><span class="sxs-lookup"><span data-stu-id="763e8-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="763e8-151">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="763e8-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="763e8-152">V předchozím kódu vrátí požadavek na platný alias autora `200 OK` odpověď s daty autora.</span><span class="sxs-lookup"><span data-stu-id="763e8-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="763e8-153">Požadavek na neplatný alias vrací `204 No Content` odpověď.</span><span class="sxs-lookup"><span data-stu-id="763e8-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="763e8-154">Hlavička Accept</span><span class="sxs-lookup"><span data-stu-id="763e8-154">The Accept header</span></span>

<span data-ttu-id="763e8-155">Po zobrazení hlavičky v požadavku se *domlouvání* obsahu provede `Accept` .</span><span class="sxs-lookup"><span data-stu-id="763e8-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="763e8-156">Když požadavek obsahuje hlavičku Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="763e8-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="763e8-157">Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="763e8-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="763e8-158">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.</span><span class="sxs-lookup"><span data-stu-id="763e8-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="763e8-159">Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="763e8-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="763e8-160">Vrátí `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> , pokud byla nastavena, nebo-</span><span class="sxs-lookup"><span data-stu-id="763e8-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="763e8-161">Pokusí se najít první formátovací modul, který může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="763e8-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="763e8-162">Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="763e8-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="763e8-163">Pokud `Accept` se v požadavku nezobrazí žádná hlavička:</span><span class="sxs-lookup"><span data-stu-id="763e8-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="763e8-164">První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="763e8-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="763e8-165">Nedochází k žádnému vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="763e8-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="763e8-166">Server určí, který formát se má vrátit.</span><span class="sxs-lookup"><span data-stu-id="763e8-166">The server is determining what format to return.</span></span>

<span data-ttu-id="763e8-167">Pokud hlavička Accept obsahuje `*/*` , záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není nastavené na hodnotu true <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="763e8-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="763e8-168">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="763e8-168">Browsers and content negotiation</span></span>

<span data-ttu-id="763e8-169">Na rozdíl od typických klientů rozhraní API webové prohlížeče poskytují `Accept` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="763e8-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="763e8-170">Webový prohlížeč určí mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="763e8-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="763e8-171">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="763e8-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="763e8-172">`Accept`Záhlaví je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="763e8-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="763e8-173">Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="763e8-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="763e8-174">Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="763e8-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="763e8-175">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí prohlížeče, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true` :</span><span class="sxs-lookup"><span data-stu-id="763e8-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="763e8-176">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="763e8-176">Configure formatters</span></span>

<span data-ttu-id="763e8-177">Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="763e8-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="763e8-178">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="763e8-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="763e8-179">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="763e8-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="763e8-180">Výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="763e8-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="763e8-181">Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="763e8-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="763e8-182">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="763e8-182">Add XML format support</span></span>

<span data-ttu-id="763e8-183">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="763e8-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="763e8-184">Předchozí kód serializace výsledků pomocí `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="763e8-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="763e8-185">Při použití předchozího kódu vrátí metody kontroleru odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="763e8-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="763e8-186">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="763e8-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="763e8-187">Funkce pro formátovací moduly založené na službě se `System.Text.Json` dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="763e8-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="763e8-188">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="763e8-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="763e8-189">Příklad:</span><span class="sxs-lookup"><span data-stu-id="763e8-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="763e8-190">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="763e8-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="763e8-191">Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku.</span><span class="sxs-lookup"><span data-stu-id="763e8-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="763e8-192">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="763e8-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="763e8-193">Podpora `Newtonsoft.Json` formátování a funkcí založených na základech je k dispozici po instalaci [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) balíčku NuGet a jeho konfiguraci v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="763e8-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="763e8-194">Některé funkce nemusí fungovat správně s `System.Text.Json` formátovacími moduly a vyžadují odkaz na `Newtonsoft.Json` formátování na základě.</span><span class="sxs-lookup"><span data-stu-id="763e8-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="763e8-195">I nadále používat formátovací moduly založené na tom, `Newtonsoft.Json` Pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="763e8-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="763e8-196">Používá `Newtonsoft.Json` atributy.</span><span class="sxs-lookup"><span data-stu-id="763e8-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="763e8-197">Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="763e8-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="763e8-198">Přizpůsobuje nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="763e8-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="763e8-199">Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="763e8-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="763e8-200">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="763e8-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="763e8-201">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje instance `JsonSerializerSettings` , která je specifická pro `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="763e8-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="763e8-202">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="763e8-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="763e8-203">Funkce pro formátovací moduly založené na službě se `Newtonsoft.Json` dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="763e8-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="763e8-204">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="763e8-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="763e8-205">Příklad:</span><span class="sxs-lookup"><span data-stu-id="763e8-205">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="763e8-206">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="763e8-206">Add XML format support</span></span>

<span data-ttu-id="763e8-207">Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="763e8-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="763e8-208">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="763e8-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="763e8-209">Předchozí kód serializace výsledků pomocí `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="763e8-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="763e8-210">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="763e8-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="763e8-211">Zadat formát</span><span class="sxs-lookup"><span data-stu-id="763e8-211">Specify a format</span></span>

<span data-ttu-id="763e8-212">Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr.</span><span class="sxs-lookup"><span data-stu-id="763e8-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="763e8-213">Podobně jako u většiny [filtrů](xref:mvc/controllers/filters) `[Produces]` lze použít akci, řadič nebo globální rozsah:</span><span class="sxs-lookup"><span data-stu-id="763e8-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="763e8-214">Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filtr:</span><span class="sxs-lookup"><span data-stu-id="763e8-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="763e8-215">Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="763e8-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="763e8-216">Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.</span><span class="sxs-lookup"><span data-stu-id="763e8-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="763e8-217">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="763e8-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="763e8-218">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="763e8-218">Special case formatters</span></span>

<span data-ttu-id="763e8-219">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="763e8-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="763e8-220">Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud jsou požadovány prostřednictvím `Accept` hlavičky).</span><span class="sxs-lookup"><span data-stu-id="763e8-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="763e8-221">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="763e8-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="763e8-222">Formátovací moduly jsou v metodě odebrány `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="763e8-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="763e8-223">Akce, které mají vrácen návratový typ objektu modelu `204 No Content` při návratu `null` .</span><span class="sxs-lookup"><span data-stu-id="763e8-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="763e8-224">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="763e8-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="763e8-225">Následující kód odstraní `StringOutputFormatter` a `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="763e8-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="763e8-226">Bez rozhraní `StringOutputFormatter` , předdefinované formáty formátování JSON `string` vrátí typy.</span><span class="sxs-lookup"><span data-stu-id="763e8-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="763e8-227">Pokud je vestavěný formátovací modul JSON odebraný a je k dispozici formátovací modul XML, vrátí typy formátování formátovací kód XML `string` .</span><span class="sxs-lookup"><span data-stu-id="763e8-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="763e8-228">V opačném případě `string` návratové typy vrátí `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="763e8-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="763e8-229">Bez `HttpNoContentOutputFormatter` objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="763e8-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="763e8-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="763e8-230">For example:</span></span>

* <span data-ttu-id="763e8-231">Formátovací modul JSON vrátí odpověď s tělem `null` .</span><span class="sxs-lookup"><span data-stu-id="763e8-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="763e8-232">Formátovací modul XML vrátí prázdný element XML s `xsi:nil="true"` nastaveným atributem.</span><span class="sxs-lookup"><span data-stu-id="763e8-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="763e8-233">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="763e8-233">Response format URL mappings</span></span>

<span data-ttu-id="763e8-234">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="763e8-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="763e8-235">V řetězci dotazu nebo v části cesty.</span><span class="sxs-lookup"><span data-stu-id="763e8-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="763e8-236">Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="763e8-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="763e8-237">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="763e8-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="763e8-238">Příklad:</span><span class="sxs-lookup"><span data-stu-id="763e8-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="763e8-239">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="763e8-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="763e8-240">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Atribut kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="763e8-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="763e8-241">Trasa</span><span class="sxs-lookup"><span data-stu-id="763e8-241">Route</span></span>        |             <span data-ttu-id="763e8-242">Modul</span><span class="sxs-lookup"><span data-stu-id="763e8-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="763e8-243">Výchozí výstupní formátovací modul</span><span class="sxs-lookup"><span data-stu-id="763e8-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="763e8-244">Formátovací modul JSON (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="763e8-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="763e8-245">Formátovací modul XML (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="763e8-245">The XML formatter (if configured)</span></span>  |
