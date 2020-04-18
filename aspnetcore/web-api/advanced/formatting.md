---
title: Formátování dat odpovědí v ASP.NET základního webového rozhraní API
author: ardalis
description: Přečtěte si, jak formátovat data odpovědí v ASP.NET základní webové rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642712"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="b24eb-103">Formátování dat odpovědí v ASP.NET základního webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="b24eb-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="b24eb-104">[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b24eb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b24eb-105">ASP.NET Core MVC podporuje formátování dat odpovědí.</span><span class="sxs-lookup"><span data-stu-id="b24eb-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="b24eb-106">Data odpovědí mohou být formátována pomocí určitých formátů nebo v reakci na formát požadovaný klientem.</span><span class="sxs-lookup"><span data-stu-id="b24eb-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="b24eb-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b24eb-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="b24eb-108">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="b24eb-108">Format-specific Action Results</span></span>

<span data-ttu-id="b24eb-109">Některé typy výsledků akce jsou specifické pro <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult>určitý formát, například a .</span><span class="sxs-lookup"><span data-stu-id="b24eb-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="b24eb-110">Akce mohou vrátit výsledky, které jsou formátovány v určitém formátu, bez ohledu na předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="b24eb-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="b24eb-111">Například vrácení `JsonResult` vrátí data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="b24eb-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="b24eb-112">Vrácení `ContentResult` nebo řetězec vrátí data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="b24eb-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="b24eb-113">Akce není nutné vrátit žádný konkrétní typ.</span><span class="sxs-lookup"><span data-stu-id="b24eb-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="b24eb-114">ASP.NET Core podporuje libovolnou vrácenou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="b24eb-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="b24eb-115">Výsledky z akcí, které <xref:Microsoft.AspNetCore.Mvc.IActionResult> vracejí objekty, které <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> nejsou typy jsou serializovány pomocí příslušné implementace.</span><span class="sxs-lookup"><span data-stu-id="b24eb-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="b24eb-116">Další informace naleznete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="b24eb-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="b24eb-117">Vestavěná pomocná <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> metoda vrátí data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="b24eb-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="b24eb-118">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="b24eb-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="b24eb-119">Použití nástrojů pro vývojáře prohlížeče F12 nebo [Postman](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="b24eb-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="b24eb-120">Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="b24eb-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="b24eb-121">Zobrazí se hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="b24eb-121">The request headers are displayed.</span></span> <span data-ttu-id="b24eb-122">Například `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b24eb-122">For example, the `Accept` header.</span></span> <span data-ttu-id="b24eb-123">Záhlaví `Accept` je ignorováno předchozím kódem.</span><span class="sxs-lookup"><span data-stu-id="b24eb-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="b24eb-124">Chcete-li vrátit data formátovaná ve formátu prostého textu, použijte <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocné ho:</span><span class="sxs-lookup"><span data-stu-id="b24eb-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="b24eb-125">V předchozím kódu vrácené `Content-Type` `text/plain`je .</span><span class="sxs-lookup"><span data-stu-id="b24eb-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="b24eb-126">Vrácení řetězec dodává `Content-Type` `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="b24eb-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="b24eb-127">Pro akce s více `IActionResult`návratovými typy vraťte .</span><span class="sxs-lookup"><span data-stu-id="b24eb-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="b24eb-128">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="b24eb-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="b24eb-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="b24eb-129">Content negotiation</span></span>

<span data-ttu-id="b24eb-130">K vyjednávání obsahu dochází, když klient určí [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="b24eb-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="b24eb-131">Výchozím formátem používaným ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="b24eb-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="b24eb-132">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="b24eb-132">Content negotiation is:</span></span>

* <span data-ttu-id="b24eb-133">Implementována společností <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="b24eb-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="b24eb-134">Zabudované do výsledků akce specifických pro stavový kód vrácené z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="b24eb-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="b24eb-135">Pomocné metody výsledků akce `ObjectResult`jsou založeny na .</span><span class="sxs-lookup"><span data-stu-id="b24eb-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="b24eb-136">Při vrácení typu modelu je `ObjectResult`návratový typ .</span><span class="sxs-lookup"><span data-stu-id="b24eb-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="b24eb-137">Následující metoda akce `Ok` používá `NotFound` a pomocné metody:</span><span class="sxs-lookup"><span data-stu-id="b24eb-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="b24eb-138">Ve výchozím nastavení `application/json`podporuje `text/json`ASP.NET `text/plain` Core typy médií a typy médií.</span><span class="sxs-lookup"><span data-stu-id="b24eb-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="b24eb-139">Nástroje jako [Fiddler](https://www.telerik.com/fiddler) nebo [Postman](https://www.getpostman.com/tools) můžete nastavit hlavičku `Accept` požadavku určit formát vrácení.</span><span class="sxs-lookup"><span data-stu-id="b24eb-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="b24eb-140">Pokud `Accept` záhlaví obsahuje typ, který server podporuje, je tento typ vrácen.</span><span class="sxs-lookup"><span data-stu-id="b24eb-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="b24eb-141">V další části se zobrazí, jak přidat další formatters.</span><span class="sxs-lookup"><span data-stu-id="b24eb-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="b24eb-142">Akce řadiče může vrátit POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="b24eb-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="b24eb-143">Když je vrácena POCO, runtime `ObjectResult` automaticky vytvoří, který obtéká objekt.</span><span class="sxs-lookup"><span data-stu-id="b24eb-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="b24eb-144">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="b24eb-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="b24eb-145">Pokud je `null`vrácený objekt `204 No Content` , je vrácena odpověď.</span><span class="sxs-lookup"><span data-stu-id="b24eb-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="b24eb-146">Vrácení typu objektu:</span><span class="sxs-lookup"><span data-stu-id="b24eb-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="b24eb-147">V předchozím kódu vrátí požadavek na platný `200 OK` alias autora odpověď s daty autora.</span><span class="sxs-lookup"><span data-stu-id="b24eb-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="b24eb-148">Požadavek na neplatný `204 No Content` alias vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="b24eb-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="b24eb-149">Hlavička Přijmout</span><span class="sxs-lookup"><span data-stu-id="b24eb-149">The Accept header</span></span>

<span data-ttu-id="b24eb-150">*Vyjednávání* obsahu probíhá, když se v žádosti zobrazí `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b24eb-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="b24eb-151">Pokud požadavek obsahuje hlavičku přijetí, ASP.NET jádro:</span><span class="sxs-lookup"><span data-stu-id="b24eb-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="b24eb-152">Vyjmenovává typy médií v záhlaví přijmout v pořadí předvoleb.</span><span class="sxs-lookup"><span data-stu-id="b24eb-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="b24eb-153">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom ze zadaných formátů.</span><span class="sxs-lookup"><span data-stu-id="b24eb-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="b24eb-154">Pokud není nalezen žádný pohovce, který může uspokojit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b24eb-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="b24eb-155">`406 Not Acceptable` Vrátí, <xref:Microsoft.AspNetCore.Mvc.MvcOptions> pokud byla nastavena, nebo -</span><span class="sxs-lookup"><span data-stu-id="b24eb-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="b24eb-156">Pokusí se najít první propojka, která může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="b24eb-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="b24eb-157">Pokud žádný formátovací modul je nakonfigurován pro požadovaný formát, první formátovací modul, který může formátovat objekt se používá.</span><span class="sxs-lookup"><span data-stu-id="b24eb-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="b24eb-158">Pokud `Accept` se v požadavku nezobrazí žádná hlavička:</span><span class="sxs-lookup"><span data-stu-id="b24eb-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="b24eb-159">První formátování, které může zpracovat objekt se používá k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b24eb-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="b24eb-160">Žádné vyjednávání se nekoná.</span><span class="sxs-lookup"><span data-stu-id="b24eb-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="b24eb-161">Server určuje, jaký formát má být vrácen.</span><span class="sxs-lookup"><span data-stu-id="b24eb-161">The server is determining what format to return.</span></span>

<span data-ttu-id="b24eb-162">Pokud hlavička `*/*`Přijmout obsahuje , je `RespectBrowserAcceptHeader` záhlaví ignorováno, pokud není v . <xref:Microsoft.AspNetCore.Mvc.MvcOptions></span><span class="sxs-lookup"><span data-stu-id="b24eb-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="b24eb-163">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="b24eb-163">Browsers and content negotiation</span></span>

<span data-ttu-id="b24eb-164">Na rozdíl od běžných `Accept` klientů rozhraní API poskytují webové prohlížeče záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b24eb-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="b24eb-165">Webový prohlížeč určuje mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="b24eb-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="b24eb-166">Ve výchozím nastavení, když rozhraní framework zjistí, že požadavek pochází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="b24eb-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="b24eb-167">Záhlaví `Accept` je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="b24eb-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="b24eb-168">Obsah je vrácen v JSON, pokud není nakonfigurováno jinak.</span><span class="sxs-lookup"><span data-stu-id="b24eb-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="b24eb-169">To poskytuje konzistentnější prostředí napříč prohlížeči při využívání api.</span><span class="sxs-lookup"><span data-stu-id="b24eb-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="b24eb-170">Chcete-li nakonfigurovat aplikaci tak, <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`aby uctila záhlaví prohlížeče, nastavte na :</span><span class="sxs-lookup"><span data-stu-id="b24eb-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="b24eb-171">Konfigurace formatters</span><span class="sxs-lookup"><span data-stu-id="b24eb-171">Configure formatters</span></span>

<span data-ttu-id="b24eb-172">Aplikace, které potřebují podporovat další formáty můžete přidat příslušné balíčky NuGet a konfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="b24eb-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="b24eb-173">Existují samostatné formatters pro vstup a výstup.</span><span class="sxs-lookup"><span data-stu-id="b24eb-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="b24eb-174">Vstupní formatters jsou používány [model vazby](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b24eb-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b24eb-175">Výstup formatters se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="b24eb-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="b24eb-176">Informace o vytvoření vlastního modulu průhlednce naleznete [v tématu Custom Formatters](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="b24eb-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="b24eb-177">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="b24eb-177">Add XML format support</span></span>

<span data-ttu-id="b24eb-178">Formáty XML <xref:System.Xml.Serialization.XmlSerializer> implementované pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>jsou konfigurovány voláním :</span><span class="sxs-lookup"><span data-stu-id="b24eb-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="b24eb-179">Předchozí kód serializuje výsledky pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="b24eb-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b24eb-180">Při použití předchozího kódu metody řadiče vrátí příslušný formát `Accept` na základě hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="b24eb-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="b24eb-181">Konfigurace formanace založené na souboru System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="b24eb-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="b24eb-182">Funkce `System.Text.Json`formatters založené lze konfigurovat `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`pomocí .</span><span class="sxs-lookup"><span data-stu-id="b24eb-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b24eb-183">Možnosti serializace výstupu pro individuální použití lze `JsonResult`konfigurovat pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="b24eb-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b24eb-184">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b24eb-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="b24eb-185">Přidat podporu formátu JSON založeného na newtonsoftu.Json</span><span class="sxs-lookup"><span data-stu-id="b24eb-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="b24eb-186">Před ASP.NET Core 3.0, výchozí použité JSON `Newtonsoft.Json` formatters implementována pomocí balíčku.</span><span class="sxs-lookup"><span data-stu-id="b24eb-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="b24eb-187">V ASP.NET jádrem 3.0 nebo novějším, výchozí `System.Text.Json`formatters JSON jsou založeny na .</span><span class="sxs-lookup"><span data-stu-id="b24eb-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="b24eb-188">Podpora `Newtonsoft.Json` formatters a funkcí založených na webu je k dispozici instalací balíčku [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet a jeho konfigurací v aplikaci `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b24eb-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="b24eb-189">Některé funkce nemusí fungovat `System.Text.Json`dobře s formatters založené `Newtonsoft.Json`a vyžadují odkaz na formatters založené na bázi.</span><span class="sxs-lookup"><span data-stu-id="b24eb-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="b24eb-190">Pokračovat v `Newtonsoft.Json`používání formatters založené na základě, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="b24eb-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="b24eb-191">Používá `Newtonsoft.Json` atributy.</span><span class="sxs-lookup"><span data-stu-id="b24eb-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="b24eb-192">Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="b24eb-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="b24eb-193">Přizpůsobí nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="b24eb-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="b24eb-194">Spoléhá na funkce, které `Newtonsoft.Json` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="b24eb-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="b24eb-195">Konfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="b24eb-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="b24eb-196">Před ASP.NET Core 3.0, přijímá `JsonSerializerSettings` instanci, `Newtonsoft.Json` `JsonResult.SerializerSettings` která je specifická pro .</span><span class="sxs-lookup"><span data-stu-id="b24eb-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="b24eb-197">Generuje dokumentaci [OpenAPI.](<xref:tutorials/web-api-help-pages-using-swagger>)</span><span class="sxs-lookup"><span data-stu-id="b24eb-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="b24eb-198">Funkce `Newtonsoft.Json`formatters založené na bázi `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`lze konfigurovat pomocí :</span><span class="sxs-lookup"><span data-stu-id="b24eb-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b24eb-199">Možnosti serializace výstupu pro individuální použití lze `JsonResult`konfigurovat pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="b24eb-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b24eb-200">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b24eb-200">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="b24eb-201">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="b24eb-201">Add XML format support</span></span>

<span data-ttu-id="b24eb-202">Formátování XML vyžaduje balíček [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="b24eb-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="b24eb-203">Formáty XML <xref:System.Xml.Serialization.XmlSerializer> implementované pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>jsou konfigurovány voláním :</span><span class="sxs-lookup"><span data-stu-id="b24eb-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="b24eb-204">Předchozí kód serializuje výsledky pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="b24eb-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b24eb-205">Při použití předchozího kódu by metody řadiče měly vrátit `Accept` příslušný formát na základě hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="b24eb-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="b24eb-206">Určení formátu</span><span class="sxs-lookup"><span data-stu-id="b24eb-206">Specify a format</span></span>

<span data-ttu-id="b24eb-207">Chcete-li omezit formáty [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) odpovědí, použijte filtr.</span><span class="sxs-lookup"><span data-stu-id="b24eb-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="b24eb-208">Stejně jako `[Produces]` [většina filtrů](xref:mvc/controllers/filters)lze použít v akci, kontroleru nebo globálním oboru:</span><span class="sxs-lookup"><span data-stu-id="b24eb-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="b24eb-209">Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:</span><span class="sxs-lookup"><span data-stu-id="b24eb-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="b24eb-210">Vynutí všechny akce v rámci řadiče vrátit odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="b24eb-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="b24eb-211">Pokud jsou nakonfigurovány jiné formatters a klient určuje jiný formát, je vrácena JSON.</span><span class="sxs-lookup"><span data-stu-id="b24eb-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="b24eb-212">Další informace naleznete v [tématu Filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="b24eb-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="b24eb-213">Zvláštní případ pro věci</span><span class="sxs-lookup"><span data-stu-id="b24eb-213">Special case formatters</span></span>

<span data-ttu-id="b24eb-214">Některé zvláštní případy jsou implementovány pomocí vestavěných formatters.</span><span class="sxs-lookup"><span data-stu-id="b24eb-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="b24eb-215">Ve výchozím `string` nastavení jsou návratové typy formátovány jako *text/prostý* *(text/html* na vyžádání přes hlavičku). `Accept`</span><span class="sxs-lookup"><span data-stu-id="b24eb-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="b24eb-216">Toto chování lze odstranit <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>odebráním .</span><span class="sxs-lookup"><span data-stu-id="b24eb-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="b24eb-217">Formatters jsou odstraněny `ConfigureServices` v metodě.</span><span class="sxs-lookup"><span data-stu-id="b24eb-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="b24eb-218">Akce, které mají objekt `204 No Content` modelu návratový typ vrátit při `null`vrácení .</span><span class="sxs-lookup"><span data-stu-id="b24eb-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="b24eb-219">Toto chování lze odstranit <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>odebráním .</span><span class="sxs-lookup"><span data-stu-id="b24eb-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="b24eb-220">Následující kód odebere `StringOutputFormatter` `HttpNoContentOutputFormatter`a .</span><span class="sxs-lookup"><span data-stu-id="b24eb-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="b24eb-221">Bez `StringOutputFormatter`, předdefinované formáty formátů formátů formátů json formáty formátů `string` návratové.</span><span class="sxs-lookup"><span data-stu-id="b24eb-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="b24eb-222">Pokud je odebrán předdefinovaný formátovací modul JSON a formát formátu XML `string` je k dispozici, formáty formátů formátů xml vrátí typy.</span><span class="sxs-lookup"><span data-stu-id="b24eb-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="b24eb-223">V `string` opačném případě `406 Not Acceptable`vrátí typy vrátí .</span><span class="sxs-lookup"><span data-stu-id="b24eb-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="b24eb-224">Bez `HttpNoContentOutputFormatter`, null objekty jsou formátovány pomocí nakonfigurované formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="b24eb-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="b24eb-225">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b24eb-225">For example:</span></span>

* <span data-ttu-id="b24eb-226">JSON forové matné modul `null`vrátí odpověď s tělo .</span><span class="sxs-lookup"><span data-stu-id="b24eb-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="b24eb-227">Formátovací modul XML vrátí prázdný element `xsi:nil="true"` XML se sadou atributů.</span><span class="sxs-lookup"><span data-stu-id="b24eb-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="b24eb-228">Mapování adres URL ve formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="b24eb-228">Response format URL mappings</span></span>

<span data-ttu-id="b24eb-229">Klienti mohou požádat o určitý formát jako součást adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="b24eb-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="b24eb-230">V řetězci dotazu nebo části cesty.</span><span class="sxs-lookup"><span data-stu-id="b24eb-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="b24eb-231">Pomocí přípony souboru specifického pro formát, například XML nebo JSON.</span><span class="sxs-lookup"><span data-stu-id="b24eb-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="b24eb-232">Mapování z cesty požadavku by mělo být zadáno v trase, kterou rozhraní API používá.</span><span class="sxs-lookup"><span data-stu-id="b24eb-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="b24eb-233">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b24eb-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="b24eb-234">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="b24eb-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="b24eb-235">Atribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) zkontroluje existenci hodnoty formátu v `RouteData` a mapuje formát odpovědi na příslušný formátovací modul při vytvoření odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b24eb-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="b24eb-236">Trasa</span><span class="sxs-lookup"><span data-stu-id="b24eb-236">Route</span></span>        |             <span data-ttu-id="b24eb-237">Formátovací modul</span><span class="sxs-lookup"><span data-stu-id="b24eb-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="b24eb-238">Výchozí výstupní formatter</span><span class="sxs-lookup"><span data-stu-id="b24eb-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="b24eb-239">JSON formovní modul (je-li nakonfigurován)</span><span class="sxs-lookup"><span data-stu-id="b24eb-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="b24eb-240">Formátovací modul XML (pokud je nakonfigurován)</span><span class="sxs-lookup"><span data-stu-id="b24eb-240">The XML formatter (if configured)</span></span>  |
