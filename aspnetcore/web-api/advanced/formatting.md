---
title: Formátování dat odpovědi v ASP.NET Core Web API
author: ardalis
description: Naučte se, jak formátovat data odpovědí v ASP.NET Core webovém rozhraní API.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 5861a8e353b8fac95ca51aca7b44a768d3c2ffb7
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71199060"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="21548-103">Formátování dat odpovědi v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="21548-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="21548-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="21548-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="21548-105">ASP.NET Core MVC podporuje formátování dat odpovědi.</span><span class="sxs-lookup"><span data-stu-id="21548-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="21548-106">Data odpovědi lze formátovat pomocí konkrétních formátů nebo v reakci na požadovaný formát klienta.</span><span class="sxs-lookup"><span data-stu-id="21548-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="21548-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="21548-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="21548-108">Výsledky akce specifické pro formát</span><span class="sxs-lookup"><span data-stu-id="21548-108">Format-specific Action Results</span></span>

<span data-ttu-id="21548-109">Některé typy výsledků akce jsou specifické pro konkrétní formát, například <xref:Microsoft.AspNetCore.Mvc.JsonResult> a. <xref:Microsoft.AspNetCore.Mvc.ContentResult></span><span class="sxs-lookup"><span data-stu-id="21548-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="21548-110">Akce mohou vracet výsledky, které jsou formátovány v určitém formátu bez ohledu na Předvolby klienta.</span><span class="sxs-lookup"><span data-stu-id="21548-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="21548-111">Například vrácení `JsonResult` vrátí data ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="21548-112">Vrácení `ContentResult` nebo řetězec vrací data řetězce ve formátu prostého textu.</span><span class="sxs-lookup"><span data-stu-id="21548-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="21548-113">Akce není nutná k vrácení určitého konkrétního typu.</span><span class="sxs-lookup"><span data-stu-id="21548-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="21548-114">ASP.NET Core podporuje návratovou hodnotu objektu.</span><span class="sxs-lookup"><span data-stu-id="21548-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="21548-115">Výsledky z akcí, které vracejí objekty, které <xref:Microsoft.AspNetCore.Mvc.IActionResult> nejsou typy, jsou serializovány <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> pomocí příslušné implementace.</span><span class="sxs-lookup"><span data-stu-id="21548-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="21548-116">Další informace naleznete v tématu <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="21548-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="21548-117">Integrovaná pomocná metoda <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> vrací data ve formátu JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="21548-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="21548-118">Ukázka stažení vrátí seznam autorů.</span><span class="sxs-lookup"><span data-stu-id="21548-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="21548-119">Pomocí nástrojů pro vývojáře v prohlížeči F12 nebo [příspěvku](https://www.getpostman.com/tools) s předchozím kódem:</span><span class="sxs-lookup"><span data-stu-id="21548-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="21548-120">Zobrazí se hlavička odpovědi obsahující **typ obsahu:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="21548-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="21548-121">Zobrazí se hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="21548-121">The request headers are displayed.</span></span> <span data-ttu-id="21548-122">Například `Accept` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="21548-122">For example, the `Accept` header.</span></span> <span data-ttu-id="21548-123">`Accept` Hlavička je ignorována v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="21548-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="21548-124">Chcete-li vrátit data ve formátu prostého textu <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> , použijte aplikaci <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> a pomocníka:</span><span class="sxs-lookup"><span data-stu-id="21548-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="21548-125">V předchozím kódu `Content-Type` je `text/plain`vráceno.</span><span class="sxs-lookup"><span data-stu-id="21548-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="21548-126">Vrácení řetězcového doručení `Content-Type`: `text/plain`</span><span class="sxs-lookup"><span data-stu-id="21548-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="21548-127">Pro akce s více návratovými typy vrátí `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="21548-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="21548-128">Například vrácení různých stavových kódů HTTP na základě výsledku provedených operací.</span><span class="sxs-lookup"><span data-stu-id="21548-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="21548-129">Vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="21548-129">Content negotiation</span></span>

<span data-ttu-id="21548-130">K domlouvání obsahu dojde, když klient zadá [hlavičku Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="21548-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="21548-131">Výchozí formát používaný ASP.NET Core je [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="21548-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="21548-132">Vyjednávání obsahu je:</span><span class="sxs-lookup"><span data-stu-id="21548-132">Content negotiation is:</span></span>

* <span data-ttu-id="21548-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult>Implementuje.</span><span class="sxs-lookup"><span data-stu-id="21548-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="21548-134">Integrováno do výsledků akce specifické pro kód stavu vrácených z pomocných metod.</span><span class="sxs-lookup"><span data-stu-id="21548-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="21548-135">Pomocné metody výsledků akcí jsou založeny na `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="21548-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="21548-136">Při vrácení typu modelu je `ObjectResult`návratový typ.</span><span class="sxs-lookup"><span data-stu-id="21548-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="21548-137">Následující metoda akce používá `Ok` pomocné metody a: `NotFound`</span><span class="sxs-lookup"><span data-stu-id="21548-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="21548-138">Odpověď ve formátu JSON se vrátí, pokud není požadován jiný formát a server může vracet požadovaný formát.</span><span class="sxs-lookup"><span data-stu-id="21548-138">A JSON-formatted response is returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="21548-139">Nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [Poster](https://www.getpostman.com/tools) , `Accept` mohou nastavit hlavičku pro určení návratového formátu.</span><span class="sxs-lookup"><span data-stu-id="21548-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` header to specify the return format.</span></span> <span data-ttu-id="21548-140">`Accept` Když obsahuje typ, který server podporuje, vrátí se tento typ.</span><span class="sxs-lookup"><span data-stu-id="21548-140">When the `Accept` contains a type the server supports, that type is returned.</span></span>

<span data-ttu-id="21548-141">Ve výchozím nastavení ASP.NET Core podporuje pouze JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-141">By default, ASP.NET Core only supports JSON.</span></span> <span data-ttu-id="21548-142">Pro aplikace, které nemění výchozí hodnoty, jsou odpovědi ve formátu JSON vždycky vráceny bez ohledu na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="21548-142">For apps not changing the default, JSON-formatted responses are alway returned regardless of the client request.</span></span> <span data-ttu-id="21548-143">V další části se dozvíte, jak přidat další formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="21548-143">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="21548-144">Akce kontroleru mohou vracet POCOs (objekty CLR ve starém formátu).</span><span class="sxs-lookup"><span data-stu-id="21548-144">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="21548-145">Když se vrátí POCO, modul runtime automaticky vytvoří `ObjectResult` objekt, který zabalí objekt.</span><span class="sxs-lookup"><span data-stu-id="21548-145">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="21548-146">Klient získá formátovaný serializovaný objekt.</span><span class="sxs-lookup"><span data-stu-id="21548-146">The client gets the formatted serialized object.</span></span> <span data-ttu-id="21548-147">Pokud je `null`vrácen objekt `204 No Content` , je vrácena odpověď.</span><span class="sxs-lookup"><span data-stu-id="21548-147">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="21548-148">Vrací se typ objektu:</span><span class="sxs-lookup"><span data-stu-id="21548-148">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="21548-149">V předchozím kódu vrátí `200 OK` požadavek na platný alias autora odpověď s daty autora.</span><span class="sxs-lookup"><span data-stu-id="21548-149">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="21548-150">Požadavek na neplatný alias vrací `204 No Content` odpověď.</span><span class="sxs-lookup"><span data-stu-id="21548-150">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="21548-151">Hlavička Accept</span><span class="sxs-lookup"><span data-stu-id="21548-151">The Accept header</span></span>

<span data-ttu-id="21548-152">Po`Accept` zobrazení hlavičky v požadavku se *domlouvání* obsahu provede.</span><span class="sxs-lookup"><span data-stu-id="21548-152">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="21548-153">Když požadavek obsahuje hlavičku Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="21548-153">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="21548-154">Vytvoří výčet typů médií v hlavičce Accept v upřednostňovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="21548-154">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="21548-155">Pokusí se najít formátovací modul, který může vytvořit odpověď v jednom z určených formátů.</span><span class="sxs-lookup"><span data-stu-id="21548-155">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="21548-156">Pokud se nenajde žádný formátovací modul, který může splnit požadavek klienta, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="21548-156">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="21548-157">Vrátí `406 Not Acceptable` , <xref:Microsoft.AspNetCore.Mvc.MvcOptions> Pokud byla nastavena, nebo-</span><span class="sxs-lookup"><span data-stu-id="21548-157">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="21548-158">Pokusí se najít první formátovací modul, který může vytvořit odpověď.</span><span class="sxs-lookup"><span data-stu-id="21548-158">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="21548-159">Pokud není pro požadovaný formát nakonfigurován žádný formátovací modul, je použit první formátovací modul, který může objekt formátovat.</span><span class="sxs-lookup"><span data-stu-id="21548-159">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="21548-160">Pokud se `Accept` v požadavku nezobrazí žádná hlavička:</span><span class="sxs-lookup"><span data-stu-id="21548-160">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="21548-161">První formátovací modul, který může zpracovat objekt, je použit k serializaci odpovědi.</span><span class="sxs-lookup"><span data-stu-id="21548-161">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="21548-162">Nedochází k žádnému vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="21548-162">There isn't any negotiation taking place.</span></span> <span data-ttu-id="21548-163">Server určí, který formát se má vrátit.</span><span class="sxs-lookup"><span data-stu-id="21548-163">The server is determining what format to return.</span></span>

<span data-ttu-id="21548-164">Pokud hlavička Accept obsahuje `*/*`, záhlaví se ignoruje, pokud `RespectBrowserAcceptHeader` <xref:Microsoft.AspNetCore.Mvc.MvcOptions>není nastavené na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="21548-164">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="21548-165">Prohlížeče a vyjednávání obsahu</span><span class="sxs-lookup"><span data-stu-id="21548-165">Browsers and content negotiation</span></span>

<span data-ttu-id="21548-166">Na rozdíl od typických klientů rozhraní API webové `Accept` prohlížeče poskytují hlavičky.</span><span class="sxs-lookup"><span data-stu-id="21548-166">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="21548-167">Webový prohlížeč určí mnoho formátů, včetně zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="21548-167">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="21548-168">Ve výchozím nastavení, když rozhraní detekuje, že požadavek přichází z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="21548-168">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="21548-169">`Accept` Záhlaví je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="21548-169">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="21548-170">Pokud není nakonfigurováno jinak, vrátí se obsah ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-170">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="21548-171">Díky tomu je v prohlížečích k dispozici jednotnější prostředí pro využívání rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="21548-171">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="21548-172">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hlavičky přijetí <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> prohlížeče `true`, nastavte na:</span><span class="sxs-lookup"><span data-stu-id="21548-172">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="21548-173">Konfigurace formátovacích modulů</span><span class="sxs-lookup"><span data-stu-id="21548-173">Configure formatters</span></span>

<span data-ttu-id="21548-174">Aplikace, které vyžadují podporu dalších formátů, můžou přidat příslušné balíčky NuGet a nakonfigurovat podporu.</span><span class="sxs-lookup"><span data-stu-id="21548-174">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="21548-175">Pro vstup a výstup existují samostatné formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="21548-175">There are separate formatters for input and output.</span></span> <span data-ttu-id="21548-176">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="21548-176">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="21548-177">Výstupní formátovací moduly se používají k formátování odpovědí.</span><span class="sxs-lookup"><span data-stu-id="21548-177">Output formatters are used to format responses.</span></span> <span data-ttu-id="21548-178">Informace o vytvoření vlastního formátovacího modulu naleznete v tématu [Custom formátovací modules](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="21548-178">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="21548-179">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="21548-179">Add XML format support</span></span>

<span data-ttu-id="21548-180">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*></span><span class="sxs-lookup"><span data-stu-id="21548-180">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="21548-181">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="21548-181">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="21548-182">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="21548-182">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="21548-183">Konfigurace formátovacích modulů založených na System. text. JSON</span><span class="sxs-lookup"><span data-stu-id="21548-183">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="21548-184">Funkce pro `System.Text.Json`formátovací moduly založené na službě se dají konfigurovat pomocí `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="21548-184">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="21548-185">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="21548-185">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="21548-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21548-186">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="21548-187">Přidání podpory formátu JSON založeného na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="21548-187">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="21548-188">Před ASP.NET Core 3,0 byly použity výchozí formátovací moduly JSON implementované pomocí `Newtonsoft.Json` balíčku.</span><span class="sxs-lookup"><span data-stu-id="21548-188">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="21548-189">V ASP.NET Core 3,0 nebo novějších jsou výchozí formátovací moduly JSON založené na `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="21548-189">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="21548-190">Podpora formátování a funkcí `Startup.ConfigureServices` založenýchnazákladechjekdispozicipoinstalacibalíčkuNuGetMicrosoft.AspNetCore.Mvc.NewtonsoftJsonajehokonfiguraciv.`Newtonsoft.Json` [](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)</span><span class="sxs-lookup"><span data-stu-id="21548-190">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="21548-191">Některé funkce nemusí fungovat správně s `System.Text.Json`formátovacími moduly a vyžadují odkaz `Newtonsoft.Json`na formátování na základě.</span><span class="sxs-lookup"><span data-stu-id="21548-191">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="21548-192">I nadále používat `Newtonsoft.Json`formátovací moduly založené na tom, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="21548-192">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="21548-193">Používá `Newtonsoft.Json` atributy.</span><span class="sxs-lookup"><span data-stu-id="21548-193">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="21548-194">Například `[JsonProperty]` nebo `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="21548-194">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="21548-195">Přizpůsobuje nastavení serializace.</span><span class="sxs-lookup"><span data-stu-id="21548-195">Customizes the serialization settings.</span></span>
* <span data-ttu-id="21548-196">Spoléhá na funkce, `Newtonsoft.Json` které poskytuje.</span><span class="sxs-lookup"><span data-stu-id="21548-196">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="21548-197">Nakonfiguruje `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="21548-197">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="21548-198">Před ASP.NET Core 3,0 `JsonResult.SerializerSettings` akceptuje `JsonSerializerSettings` instance, která je specifická pro `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="21548-198">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="21548-199">Generuje dokumentaci k [openapi](<xref:tutorials/web-api-help-pages-using-swagger>) .</span><span class="sxs-lookup"><span data-stu-id="21548-199">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="21548-200">Funkce pro `Newtonsoft.Json`formátovací moduly založené na službě se dají nakonfigurovat pomocí `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="21548-200">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="21548-201">Možnosti výstupní serializace na základě jednotlivých akcí lze konfigurovat pomocí `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="21548-201">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="21548-202">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21548-202">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="21548-203">Přidat podporu formátu XML</span><span class="sxs-lookup"><span data-stu-id="21548-203">Add XML format support</span></span>

<span data-ttu-id="21548-204">Formátování XML vyžaduje balíček NuGet [Microsoft. AspNetCore. Mvc. formátovací modul. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="21548-204">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="21548-205">Formátovací moduly XML implementované pomocí <xref:System.Xml.Serialization.XmlSerializer> jsou konfigurovány voláním: <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*></span><span class="sxs-lookup"><span data-stu-id="21548-205">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="21548-206">Předchozí kód serializace výsledků pomocí `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="21548-206">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="21548-207">Při použití předchozího kódu by metody kontroleru měly vracet odpovídající formát na základě `Accept` hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="21548-207">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="21548-208">Zadat formát</span><span class="sxs-lookup"><span data-stu-id="21548-208">Specify a format</span></span>

<span data-ttu-id="21548-209">Chcete-li omezit formáty odpovědi, použijte [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr.</span><span class="sxs-lookup"><span data-stu-id="21548-209">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="21548-210">Podobně jako [](xref:mvc/controllers/filters)u většiny `[Produces]` filtrů lze použít akci, řadič nebo globální rozsah:</span><span class="sxs-lookup"><span data-stu-id="21548-210">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="21548-211">Předchozí [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtr:</span><span class="sxs-lookup"><span data-stu-id="21548-211">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="21548-212">Vynutí, aby všechny akce v rámci kontroleru vracely odpovědi ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-212">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="21548-213">Pokud jsou nakonfigurovány jiné formátovací moduly a klient určí jiný formát, bude vrácen kód JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-213">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="21548-214">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="21548-214">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="21548-215">Formátování speciálních případů</span><span class="sxs-lookup"><span data-stu-id="21548-215">Special case formatters</span></span>

<span data-ttu-id="21548-216">Některé speciální případy jsou implementovány pomocí integrovaných formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="21548-216">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="21548-217">Ve výchozím nastavení `string` jsou návratové typy formátovány jako *textové/jednoduché* (*text/HTML* , pokud `Accept` jsou požadovány prostřednictvím hlavičky).</span><span class="sxs-lookup"><span data-stu-id="21548-217">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="21548-218">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="21548-218">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>.</span></span> <span data-ttu-id="21548-219">Formátovací moduly jsou v `Configure` metodě odebrány.</span><span class="sxs-lookup"><span data-stu-id="21548-219">Formatters are removed in the `Configure` method.</span></span> <span data-ttu-id="21548-220">Akce, které mají vrácen návratový typ `204 No Content` objektu modelu při návratu. `null`</span><span class="sxs-lookup"><span data-stu-id="21548-220">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="21548-221">Toto chování lze odstranit odebráním <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="21548-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="21548-222">Následující kód odstraní `TextOutputFormatter` a `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="21548-222">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="21548-223">Bez rozhraní `TextOutputFormatter` `string` vrátí návratové typy `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="21548-223">Without the `TextOutputFormatter`, `string` return types return `406 Not Acceptable`.</span></span> <span data-ttu-id="21548-224">Pokud formátovací modul XML existuje, formátuje `string` návratové typy, `TextOutputFormatter` Pokud je odebrán.</span><span class="sxs-lookup"><span data-stu-id="21548-224">If an XML formatter exists, it formats `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="21548-225">`HttpNoContentOutputFormatter`Bez objektů jsou objekty null formátovány pomocí nakonfigurovaného formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="21548-225">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="21548-226">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21548-226">For example:</span></span>

* <span data-ttu-id="21548-227">Formátovací modul JSON vrátí odpověď s tělem `null`.</span><span class="sxs-lookup"><span data-stu-id="21548-227">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="21548-228">Formátovací modul XML vrátí prázdný element XML s nastaveným atributem `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="21548-228">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="21548-229">Mapování adres URL formátu odpovědi</span><span class="sxs-lookup"><span data-stu-id="21548-229">Response format URL mappings</span></span>

<span data-ttu-id="21548-230">Klienti mohou požádat o konkrétní formát v rámci adresy URL, například:</span><span class="sxs-lookup"><span data-stu-id="21548-230">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="21548-231">V řetězci dotazu nebo v části cesty.</span><span class="sxs-lookup"><span data-stu-id="21548-231">In the query string or part of the path.</span></span>
* <span data-ttu-id="21548-232">Pomocí přípony souboru specifické pro formát, jako je například. XML nebo. JSON.</span><span class="sxs-lookup"><span data-stu-id="21548-232">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="21548-233">Mapování z cesty požadavku musí být zadáno v trase, kterou používá rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="21548-233">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="21548-234">Příklad:</span><span class="sxs-lookup"><span data-stu-id="21548-234">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="21548-235">Předchozí trasa umožňuje zadat požadovaný formát jako volitelnou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="21548-235">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="21548-236">Atribut kontroluje existenci hodnoty formátu `RouteData` v a při vytvoření odpovědi mapuje formát odpovědi na příslušný formátovací modul. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)</span><span class="sxs-lookup"><span data-stu-id="21548-236">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="21548-237">Cestě</span><span class="sxs-lookup"><span data-stu-id="21548-237">Route</span></span>        |             <span data-ttu-id="21548-238">Modul</span><span class="sxs-lookup"><span data-stu-id="21548-238">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="21548-239">Výchozí výstupní formátovací modul</span><span class="sxs-lookup"><span data-stu-id="21548-239">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="21548-240">Formátovací modul JSON (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="21548-240">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="21548-241">Formátovací modul XML (Pokud je nakonfigurovaný)</span><span class="sxs-lookup"><span data-stu-id="21548-241">The XML formatter (if configured)</span></span>  |
