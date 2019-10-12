---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 0dd8b3b5ec58a199db086c4c0b0f057d26afd589
ms.sourcegitcommit: 7a2c820692f04bfba04398641b70f27fa15391f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290629"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="5f1e8-103">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="5f1e8-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="5f1e8-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5f1e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5f1e8-105">ASP.NET Core MVC podporuje formátování dat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="5f1e8-106">Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="5f1e8-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f1e8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="5f1e8-108">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="5f1e8-108">Format-specific Action Results</span></span>

<span data-ttu-id="5f1e8-109">Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="5f1e8-110">Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="5f1e8-111">Například vrácení `JsonResult` vrací data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="5f1e8-112">Vrácení `ContentResult` nebo řetězce vrátí data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="5f1e8-113">Akce není nutná k vrácení určitého konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="5f1e8-114">ASP.NET Core podporuje návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="5f1e8-115">Výsledky z akcí, které vracejí objekty, které nejsou <xref:Microsoft.AspNetCore.Mvc.IActionResult>, jsou serializovány pomocí příslušné implementace <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="5f1e8-116">Další informace najdete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="5f1e8-117">Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrátí data ve formátu JSON: [!code-csharp @ no__t-2</span><span class="sxs-lookup"><span data-stu-id="5f1e8-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="5f1e8-118">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="5f1e8-119">Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="5f1e8-120">Zobrazí se hlavička odpovědi obsahující **Content-Type:** `application/json; charset=utf-8`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="5f1e8-121">Zobrazí se hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-121">The request headers are displayed.</span></span> <span data-ttu-id="5f1e8-122">Například hlavička `Accept`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-122">For example, the `Accept` header.</span></span> <span data-ttu-id="5f1e8-123">Předchozí kód ignoruje hlavičku `Accept`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="5f1e8-124">Chcete-li vrátit data ve formátu prostého textu, použijte <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a pomocníka <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="5f1e8-125">V předchozím kódu je vráceno `Content-Type` `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="5f1e8-126">Vrácení řetězce poskytuje `Content-Type` `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="5f1e8-127">Pro akce s více návratovými typy vrátí `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="5f1e8-128">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="5f1e8-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="5f1e8-129">Content negotiation</span></span>

<span data-ttu-id="5f1e8-130">K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="5f1e8-131">Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="5f1e8-132">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-132">Content negotiation is:</span></span>

* <span data-ttu-id="5f1e8-133">Implementováno pomocí <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="5f1e8-134">Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="5f1e8-135">Pomocné metody výsledků akcí jsou založené na `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="5f1e8-136">Při vrácení typu modelu je návratový typ `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="5f1e8-137">Následující metoda akce používá pomocné metody `Ok` a `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="5f1e8-138">Ve výchozím nastavení ASP.NET Core podporuje typy médií `application/json`, `text/json` a `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="5f1e8-139">Nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , můžou nastavit hlavičku požadavku `Accept` na zadání návratového formátu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="5f1e8-140">Pokud hlavička `Accept` obsahuje typ, který server podporuje, vrátí se tento typ.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="5f1e8-141">V další části se dozvíte, jak přidat další formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="5f1e8-142">Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="5f1e8-143">Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult`, který tento objekt zabalí.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="5f1e8-144">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="5f1e8-145">Pokud je vrácen objekt `null`, je vrácena odpověď `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="5f1e8-146">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="5f1e8-147">V předchozím kódu vrátí požadavek na platný alias autora odpověď `200 OK` s daty autora.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="5f1e8-148">Požadavek na neplatný alias vrátí odpověď `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="5f1e8-149">Hlavička Accept</span><span class="sxs-lookup"><span data-stu-id="5f1e8-149">The Accept header</span></span>

<span data-ttu-id="5f1e8-150">Pokud se v požadavku objeví hlavička `Accept`, bude provedeno *vyjednávání* obsahu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="5f1e8-151">Když požadavek obsahuje hlavičku Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="5f1e8-152">Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="5f1e8-153">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="5f1e8-154">Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="5f1e8-155">Vrátí `406 Not Acceptable`, pokud byl nastaven <xref:Microsoft.AspNetCore.Mvc.MvcOptions>, nebo-</span><span class="sxs-lookup"><span data-stu-id="5f1e8-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="5f1e8-156">Pokusí se najít první formátovací modul, který může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="5f1e8-157">Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="5f1e8-158">Pokud se v požadavku nezobrazí hlavička `Accept`:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="5f1e8-159">První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="5f1e8-160">Nedochází k žádnému vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="5f1e8-161">Server určí, který formát se má vrátit.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-161">The server is determining what format to return.</span></span>

<span data-ttu-id="5f1e8-162">Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` není v <xref:Microsoft.AspNetCore.Mvc.MvcOptions> nastaveno na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="5f1e8-163">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="5f1e8-163">Browsers and content negotiation</span></span>

<span data-ttu-id="5f1e8-164">Na rozdíl od typických klientů rozhraní API poskytují webové prohlížeče hlavičky `Accept`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="5f1e8-165">Webový prohlížeč určí mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="5f1e8-166">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="5f1e8-167">Hlavička `Accept` je ignorována.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="5f1e8-168">Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="5f1e8-169">Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="5f1e8-170">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala odpovědi prohlížeče, nastavte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> na `true`:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="5f1e8-171">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="5f1e8-171">Configure formatters</span></span>

<span data-ttu-id="5f1e8-172">Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="5f1e8-173">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="5f1e8-174">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="5f1e8-175">Výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="5f1e8-176">Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="5f1e8-177">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="5f1e8-177">Add XML format support</span></span>

<span data-ttu-id="5f1e8-178">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="5f1e8-179">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="5f1e8-180">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě hlavičky `Accept` požadavku.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-180">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="5f1e8-181">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="5f1e8-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="5f1e8-182">Funkce pro @no__t formátovací moduly založené na -0 se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="5f1e8-183">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="5f1e8-184">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="5f1e8-185">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="5f1e8-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="5f1e8-186">Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí balíčku `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="5f1e8-187">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="5f1e8-188">Podpora formátování a funkcí založených na @no__t 0 je k dispozici po instalaci balíčku NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) a jeho konfiguraci v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="5f1e8-189">Některé funkce nemusí fungovat dobře s @no__t formátovacími moduly založenými na -0 a vyžadují odkaz na @no__t formátovací modul založený na -1.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="5f1e8-190">Nadále používat @no__t formátovací moduly založené na -0, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="5f1e8-191">Používá `Newtonsoft.Json` atributů.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="5f1e8-192">Například `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="5f1e8-193">Přizpůsobuje nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="5f1e8-194">Spoléhá na funkce, které poskytuje `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="5f1e8-195">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="5f1e8-196">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` přijímá instanci `JsonSerializerSettings`, která je specifická pro `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="5f1e8-197">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="5f1e8-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="5f1e8-198">Funkce pro @no__t formátovací moduly založené na -0 se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="5f1e8-199">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="5f1e8-200">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-200">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="5f1e8-201">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="5f1e8-201">Add XML format support</span></span>

<span data-ttu-id="5f1e8-202">Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="5f1e8-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="5f1e8-203">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="5f1e8-204">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="5f1e8-205">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě hlavičky `Accept` požadavku.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="5f1e8-206">Zadat formát</span><span class="sxs-lookup"><span data-stu-id="5f1e8-206">Specify a format</span></span>

<span data-ttu-id="5f1e8-207">Chcete-li omezit formáty odpovědi, použijte filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="5f1e8-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="5f1e8-208">Podobně jako u většiny [filtrů](xref:mvc/controllers/filters)`[Produces]` lze použít na akci, řadič nebo globální rozsah:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="5f1e8-209">Předchozí filtr [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) :</span><span class="sxs-lookup"><span data-stu-id="5f1e8-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="5f1e8-210">Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="5f1e8-211">Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="5f1e8-212">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="5f1e8-213">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="5f1e8-213">Special case formatters</span></span>

<span data-ttu-id="5f1e8-214">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="5f1e8-215">Ve výchozím nastavení jsou návratové typy `string` formátovány jako *textové/jednoduché* (*text/HTML* , pokud jsou požadovány prostřednictvím hlavičky `Accept`).</span><span class="sxs-lookup"><span data-stu-id="5f1e8-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="5f1e8-216">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>.</span></span> <span data-ttu-id="5f1e8-217">Formátovací moduly jsou odebrány v metodě `Configure`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-217">Formatters are removed in the `Configure` method.</span></span> <span data-ttu-id="5f1e8-218">Akce, které mají návratový typ Object, vrátí `204 No Content` při vrácení `null`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="5f1e8-219">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="5f1e8-220">Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-220">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="5f1e8-221">Bez `TextOutputFormatter` `string` návratové typy vrátí `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-221">Without the `TextOutputFormatter`, `string` return types return `406 Not Acceptable`.</span></span> <span data-ttu-id="5f1e8-222">Pokud formátovací modul XML existuje, formátuje návratové typy `string`, pokud je odebráno `TextOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-222">If an XML formatter exists, it formats `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="5f1e8-223">Bez `HttpNoContentOutputFormatter` jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-223">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="5f1e8-224">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-224">For example:</span></span>

* <span data-ttu-id="5f1e8-225">Formátovací modul JSON vrátí odpověď s tělem `null`.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-225">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="5f1e8-226">Formátovací modul XML vrátí prázdný element XML s atributem `xsi:nil="true"` nastavenou.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-226">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="5f1e8-227">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="5f1e8-227">Response format URL mappings</span></span>

<span data-ttu-id="5f1e8-228">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-228">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="5f1e8-229">V řetězci dotazu nebo v části cesty.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-229">In the query string or part of the path.</span></span>
* <span data-ttu-id="5f1e8-230">Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-230">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="5f1e8-231">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-231">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="5f1e8-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f1e8-232">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="5f1e8-233">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-233">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="5f1e8-234">Atribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) kontroluje existenci hodnoty formátu v `RouteData` a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="5f1e8-234">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="5f1e8-235">Cestě</span><span class="sxs-lookup"><span data-stu-id="5f1e8-235">Route</span></span>        |             <span data-ttu-id="5f1e8-236">Modul</span><span class="sxs-lookup"><span data-stu-id="5f1e8-236">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="5f1e8-237">Výchozí výstupní formátovací modul</span><span class="sxs-lookup"><span data-stu-id="5f1e8-237">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="5f1e8-238">Formátovací modul JSON (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="5f1e8-238">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="5f1e8-239">Formátovací modul XML (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="5f1e8-239">The XML formatter (if configured)</span></span>  |
