---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 22787b20879c3739ee8a8d74c7a39e7cf8f4d5b0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774233"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="740f3-103">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="740f3-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="740f3-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="740f3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="740f3-105">ASP.NET Core MVC podporuje formátování dat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="740f3-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="740f3-106">Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.</span><span class="sxs-lookup"><span data-stu-id="740f3-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="740f3-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="740f3-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="740f3-108">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="740f3-108">Format-specific Action Results</span></span>

<span data-ttu-id="740f3-109">Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a. <xref:Microsoft.AspNetCore.Mvc.ContentResult></span><span class="sxs-lookup"><span data-stu-id="740f3-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="740f3-110">Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="740f3-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="740f3-111">Například vrácení `JsonResult` vrátí data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="740f3-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="740f3-112">Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="740f3-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="740f3-113">Akce není nutná k vrácení určitého konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="740f3-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="740f3-114">ASP.NET Core podporuje návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="740f3-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="740f3-115">Výsledky z akcí, které vracejí objekty, které <xref:Microsoft.AspNetCore.Mvc.IActionResult> nejsou typy, jsou serializovány <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> pomocí příslušné implementace.</span><span class="sxs-lookup"><span data-stu-id="740f3-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="740f3-116">Další informace naleznete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="740f3-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="740f3-117">Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="740f3-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="740f3-118">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="740f3-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="740f3-119">Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="740f3-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="740f3-120">Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="740f3-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="740f3-121">Zobrazí se hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="740f3-121">The request headers are displayed.</span></span> <span data-ttu-id="740f3-122">Například `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="740f3-122">For example, the `Accept` header.</span></span> <span data-ttu-id="740f3-123">`Accept` Hlavička je ignorována v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="740f3-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="740f3-124">Chcete-li vrátit data ve formátu prostého textu, použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> pomocníka:</span><span class="sxs-lookup"><span data-stu-id="740f3-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="740f3-125">V předchozím kódu je `Content-Type` `text/plain`vráceno.</span><span class="sxs-lookup"><span data-stu-id="740f3-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="740f3-126">Vrácení řetězcového doručení `Content-Type` `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="740f3-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="740f3-127">Pro akce s více návratovými typy vrátí `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="740f3-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="740f3-128">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="740f3-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="740f3-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="740f3-129">Content negotiation</span></span>

<span data-ttu-id="740f3-130">K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="740f3-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="740f3-131">Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="740f3-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="740f3-132">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="740f3-132">Content negotiation is:</span></span>

* <span data-ttu-id="740f3-133">Implementuje <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="740f3-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="740f3-134">Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="740f3-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="740f3-135">Pomocné metody výsledků akcí jsou založeny na `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="740f3-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="740f3-136">Při vrácení typu modelu je `ObjectResult`návratový typ.</span><span class="sxs-lookup"><span data-stu-id="740f3-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="740f3-137">Následující metoda akce používá pomocné metody `Ok` a `NotFound` :</span><span class="sxs-lookup"><span data-stu-id="740f3-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="740f3-138">Ve výchozím nastavení ASP.NET Core podporuje `application/json`, `text/json`a `text/plain` typy médií.</span><span class="sxs-lookup"><span data-stu-id="740f3-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="740f3-139">Nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , `Accept` mohou nastavit hlavičku požadavku na zadání návratového formátu.</span><span class="sxs-lookup"><span data-stu-id="740f3-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="740f3-140">Pokud `Accept` hlavička obsahuje typ, který server podporuje, vrátí se tento typ.</span><span class="sxs-lookup"><span data-stu-id="740f3-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="740f3-141">V další části se dozvíte, jak přidat další formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="740f3-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="740f3-142">Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu).</span><span class="sxs-lookup"><span data-stu-id="740f3-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="740f3-143">Když se vrátí POCO, modul runtime automaticky vytvoří objekt `ObjectResult` , který zabalí objekt.</span><span class="sxs-lookup"><span data-stu-id="740f3-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="740f3-144">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="740f3-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="740f3-145">Pokud je `null`vrácen objekt, je vrácena `204 No Content` odpověď.</span><span class="sxs-lookup"><span data-stu-id="740f3-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="740f3-146">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="740f3-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="740f3-147">V předchozím kódu vrátí požadavek na platný alias autora `200 OK` odpověď s daty autora.</span><span class="sxs-lookup"><span data-stu-id="740f3-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="740f3-148">Požadavek na neplatný alias vrací `204 No Content` odpověď.</span><span class="sxs-lookup"><span data-stu-id="740f3-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="740f3-149">Hlavička Accept</span><span class="sxs-lookup"><span data-stu-id="740f3-149">The Accept header</span></span>

<span data-ttu-id="740f3-150">Po zobrazení `Accept` hlavičky v požadavku se *domlouvání* obsahu provede.</span><span class="sxs-lookup"><span data-stu-id="740f3-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="740f3-151">Když požadavek obsahuje hlavičku Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="740f3-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="740f3-152">Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="740f3-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="740f3-153">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.</span><span class="sxs-lookup"><span data-stu-id="740f3-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="740f3-154">Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="740f3-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="740f3-155">Vrátí `406 Not Acceptable` , <xref:Microsoft.AspNetCore.Mvc.MvcOptions> Pokud byla nastavena, nebo-</span><span class="sxs-lookup"><span data-stu-id="740f3-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="740f3-156">Pokusí se najít první formátovací modul, který může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="740f3-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="740f3-157">Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="740f3-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="740f3-158">Pokud se `Accept` v požadavku nezobrazí žádná hlavička:</span><span class="sxs-lookup"><span data-stu-id="740f3-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="740f3-159">První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="740f3-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="740f3-160">Nedochází k žádnému vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="740f3-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="740f3-161">Server určí, který formát se má vrátit.</span><span class="sxs-lookup"><span data-stu-id="740f3-161">The server is determining what format to return.</span></span>

<span data-ttu-id="740f3-162">Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není nastavené na hodnotu <xref:Microsoft.AspNetCore.Mvc.MvcOptions>true.</span><span class="sxs-lookup"><span data-stu-id="740f3-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="740f3-163">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="740f3-163">Browsers and content negotiation</span></span>

<span data-ttu-id="740f3-164">Na rozdíl od typických klientů rozhraní API webové `Accept` prohlížeče poskytují hlavičky.</span><span class="sxs-lookup"><span data-stu-id="740f3-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="740f3-165">Webový prohlížeč určí mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="740f3-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="740f3-166">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="740f3-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="740f3-167">`Accept` Záhlaví je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="740f3-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="740f3-168">Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="740f3-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="740f3-169">Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="740f3-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="740f3-170">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> prohlížeče `true`, nastavte na:</span><span class="sxs-lookup"><span data-stu-id="740f3-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="740f3-171">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="740f3-171">Configure formatters</span></span>

<span data-ttu-id="740f3-172">Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="740f3-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="740f3-173">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="740f3-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="740f3-174">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="740f3-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="740f3-175">Výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="740f3-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="740f3-176">Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="740f3-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="740f3-177">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="740f3-177">Add XML format support</span></span>

<span data-ttu-id="740f3-178">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="740f3-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="740f3-179">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="740f3-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="740f3-180">Při použití předchozího kódu vrátí metody kontroleru odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="740f3-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="740f3-181">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="740f3-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="740f3-182">Funkce pro `System.Text.Json`formátovací moduly založené na službě se dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="740f3-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="740f3-183">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="740f3-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="740f3-184">Příklad:</span><span class="sxs-lookup"><span data-stu-id="740f3-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="740f3-185">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="740f3-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="740f3-186">Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku.</span><span class="sxs-lookup"><span data-stu-id="740f3-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="740f3-187">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="740f3-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="740f3-188">Podpora formátování `Newtonsoft.Json` a funkcí založených na základech je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a `Startup.ConfigureServices`jeho konfiguraci v.</span><span class="sxs-lookup"><span data-stu-id="740f3-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="740f3-189">Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz na `Newtonsoft.Json`formátování na základě.</span><span class="sxs-lookup"><span data-stu-id="740f3-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="740f3-190">I nadále používat `Newtonsoft.Json`formátovací moduly založené na tom, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="740f3-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="740f3-191">Používá `Newtonsoft.Json` atributy.</span><span class="sxs-lookup"><span data-stu-id="740f3-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="740f3-192">Příkladem je `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="740f3-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="740f3-193">Přizpůsobuje nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="740f3-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="740f3-194">Spoléhá na funkce, `Newtonsoft.Json` které poskytuje.</span><span class="sxs-lookup"><span data-stu-id="740f3-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="740f3-195">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="740f3-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="740f3-196">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje instance `JsonSerializerSettings` , která je specifická pro. `Newtonsoft.Json`</span><span class="sxs-lookup"><span data-stu-id="740f3-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="740f3-197">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="740f3-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="740f3-198">Funkce pro `Newtonsoft.Json`formátovací moduly založené na službě se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="740f3-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="740f3-199">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="740f3-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="740f3-200">Příklad:</span><span class="sxs-lookup"><span data-stu-id="740f3-200">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="740f3-201">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="740f3-201">Add XML format support</span></span>

<span data-ttu-id="740f3-202">Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="740f3-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="740f3-203">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="740f3-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="740f3-204">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="740f3-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="740f3-205">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="740f3-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="740f3-206">Zadat formát</span><span class="sxs-lookup"><span data-stu-id="740f3-206">Specify a format</span></span>

<span data-ttu-id="740f3-207">Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr.</span><span class="sxs-lookup"><span data-stu-id="740f3-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="740f3-208">Podobně jako [Filters](xref:mvc/controllers/filters)u většiny `[Produces]` filtrů lze použít akci, řadič nebo globální rozsah:</span><span class="sxs-lookup"><span data-stu-id="740f3-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="740f3-209">Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:</span><span class="sxs-lookup"><span data-stu-id="740f3-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="740f3-210">Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="740f3-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="740f3-211">Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.</span><span class="sxs-lookup"><span data-stu-id="740f3-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="740f3-212">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="740f3-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="740f3-213">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="740f3-213">Special case formatters</span></span>

<span data-ttu-id="740f3-214">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="740f3-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="740f3-215">Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud `Accept` jsou požadovány prostřednictvím hlavičky).</span><span class="sxs-lookup"><span data-stu-id="740f3-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="740f3-216">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="740f3-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="740f3-217">Formátovací moduly jsou v `ConfigureServices` metodě odebrány.</span><span class="sxs-lookup"><span data-stu-id="740f3-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="740f3-218">Akce, které mají vrácen návratový typ objektu modelu `204 No Content` při návratu `null`.</span><span class="sxs-lookup"><span data-stu-id="740f3-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="740f3-219">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="740f3-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="740f3-220">Následující kód odstraní `StringOutputFormatter` a `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="740f3-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="740f3-221">Bez rozhraní `StringOutputFormatter`, předdefinované formáty `string` formátování JSON vrátí typy.</span><span class="sxs-lookup"><span data-stu-id="740f3-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="740f3-222">Pokud je vestavěný formátovací modul JSON odebraný a je k dispozici formátovací modul XML, vrátí typy formátování `string` formátovací kód XML.</span><span class="sxs-lookup"><span data-stu-id="740f3-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="740f3-223">V opačném případě `string` návratové typy vrátí `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="740f3-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="740f3-224">`HttpNoContentOutputFormatter`Bez objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="740f3-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="740f3-225">Příklad:</span><span class="sxs-lookup"><span data-stu-id="740f3-225">For example:</span></span>

* <span data-ttu-id="740f3-226">Formátovací modul JSON vrátí odpověď s tělem `null`.</span><span class="sxs-lookup"><span data-stu-id="740f3-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="740f3-227">Formátovací modul XML vrátí prázdný element XML s nastaveným atributem `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="740f3-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="740f3-228">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="740f3-228">Response format URL mappings</span></span>

<span data-ttu-id="740f3-229">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="740f3-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="740f3-230">V řetězci dotazu nebo v části cesty.</span><span class="sxs-lookup"><span data-stu-id="740f3-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="740f3-231">Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="740f3-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="740f3-232">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="740f3-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="740f3-233">Příklad:</span><span class="sxs-lookup"><span data-stu-id="740f3-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="740f3-234">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="740f3-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="740f3-235">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) Atribut kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="740f3-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="740f3-236">Trasa</span><span class="sxs-lookup"><span data-stu-id="740f3-236">Route</span></span>        |             <span data-ttu-id="740f3-237">Modul</span><span class="sxs-lookup"><span data-stu-id="740f3-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="740f3-238">Výchozí výstupní formátovací modul</span><span class="sxs-lookup"><span data-stu-id="740f3-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="740f3-239">Formátovací modul JSON (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="740f3-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="740f3-240">Formátovací modul XML (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="740f3-240">The XML formatter (if configured)</span></span>  |
