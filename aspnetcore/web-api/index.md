---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Naučte se základy vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/11/2019
uid: web-api/index
ms.openlocfilehash: 334e5732269921a62356e7854824deccc051c291
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165169"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="95efd-103">Vytvoření webového rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95efd-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="95efd-104">Podle [Scott Addie](https://github.com/scottaddie) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="95efd-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="95efd-105">Podporuje vytváření služby RESTful, označované také jako webová rozhraní API, pomocí ASP.NET Core C#.</span><span class="sxs-lookup"><span data-stu-id="95efd-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="95efd-106">Webové rozhraní API pro zpracování žádostí, používá řadiče.</span><span class="sxs-lookup"><span data-stu-id="95efd-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="95efd-107">*Řadiče* ve webovém rozhraní API jsou třídy, které jsou odvozeny z `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="95efd-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="95efd-108">Tento článek ukazuje, jak používat kontrolery pro zpracování požadavků rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="95efd-108">This article shows how to use controllers for handling API requests.</span></span>

<span data-ttu-id="95efd-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="95efd-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="95efd-110">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="95efd-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="95efd-111">Třída ControllerBase</span><span class="sxs-lookup"><span data-stu-id="95efd-111">ControllerBase class</span></span>

<span data-ttu-id="95efd-112">Webové rozhraní API má jednu nebo více tříd kontroleru, které jsou odvozeny z <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="95efd-112">A web API has one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="95efd-113">Například šablony projektu webového rozhraní API vytvoří kontroler hodnoty:</span><span class="sxs-lookup"><span data-stu-id="95efd-113">For example, the web API project template creates a Values controller:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<span data-ttu-id="95efd-114">Nevytvářejte odvozením z kontroleru webového rozhraní API <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy.</span><span class="sxs-lookup"><span data-stu-id="95efd-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class.</span></span> <span data-ttu-id="95efd-115">`Controller` je odvozen od `ControllerBase` a přidává podporu pro zobrazení, tak, aby byl pro zpracování webové stránky, webové rozhraní API požaduje.</span><span class="sxs-lookup"><span data-stu-id="95efd-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span>  <span data-ttu-id="95efd-116">Existuje výjimka tohoto pravidla: Pokud máte v plánu používat stejný kontrolér pro zobrazení a rozhraní API, jsou odvozeny z `Controller`.</span><span class="sxs-lookup"><span data-stu-id="95efd-116">There's an exception to this rule: if you plan to use the same controller for both views and APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="95efd-117">`ControllerBase` Třída poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování žádostí HTTP.</span><span class="sxs-lookup"><span data-stu-id="95efd-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="95efd-118">Například `ControllerBase.CreatedAtAction` vrátí 201 stavový kód:</span><span class="sxs-lookup"><span data-stu-id="95efd-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 <span data-ttu-id="95efd-119">Tady jsou některé další příklady metod, které `ControllerBase` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="95efd-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="95efd-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="95efd-120">Method</span></span>  |<span data-ttu-id="95efd-121">Poznámky</span><span class="sxs-lookup"><span data-stu-id="95efd-121">Notes</span></span>  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="95efd-122">Vrátí stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="95efd-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |<span data-ttu-id="95efd-123">Vrátí stavový kód 404.</span><span class="sxs-lookup"><span data-stu-id="95efd-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="95efd-124">Vrátí soubor.</span><span class="sxs-lookup"><span data-stu-id="95efd-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="95efd-125">Vyvolá [vazby modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="95efd-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="95efd-126">Vyvolá [ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="95efd-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="95efd-127">Seznam všech dostupných metod a vlastností najdete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="95efd-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="95efd-128">Atributy</span><span class="sxs-lookup"><span data-stu-id="95efd-128">Attributes</span></span>

<span data-ttu-id="95efd-129"><xref:Microsoft.AspNetCore.Mvc> Obor názvů obsahuje atributy, které můžete použít ke konfiguraci chování kontrolerů webového rozhraní API a metod akcí.</span><span class="sxs-lookup"><span data-stu-id="95efd-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="95efd-130">Následující příklad používá atributů k určení metoda HTTP přijetí a vrátil kód stavu:</span><span class="sxs-lookup"><span data-stu-id="95efd-130">The following example uses attributes to specify the HTTP method accepted and the status codes returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="95efd-131">Tady jsou některé další příklady atributy, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="95efd-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="95efd-132">Atribut</span><span class="sxs-lookup"><span data-stu-id="95efd-132">Attribute</span></span>|<span data-ttu-id="95efd-133">Poznámky</span><span class="sxs-lookup"><span data-stu-id="95efd-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="95efd-134">[[Trasy]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="95efd-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="95efd-135">Určuje vzor adresy URL pro kontroler nebo akce.</span><span class="sxs-lookup"><span data-stu-id="95efd-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="95efd-136">[[Vazba]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="95efd-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="95efd-137">Určuje předponu a vlastnosti, které chcete zahrnout pro vazbu modelu.</span><span class="sxs-lookup"><span data-stu-id="95efd-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="95efd-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="95efd-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="95efd-139">Určuje akci, která podporuje metodu GET protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="95efd-139">Identifies an action that supports the HTTP GET method.</span></span>|
|<span data-ttu-id="95efd-140">[[Využívá]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="95efd-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="95efd-141">Určuje typy dat, které přijímá akci.</span><span class="sxs-lookup"><span data-stu-id="95efd-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="95efd-142">[[Vytváří]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="95efd-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="95efd-143">Určuje typy dat, které vrátí akci.</span><span class="sxs-lookup"><span data-stu-id="95efd-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="95efd-144">Seznam, který obsahuje dostupné atributy, najdete v článku <xref:Microsoft.AspNetCore.Mvc> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="95efd-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="95efd-145">Atribut objektu ApiController</span><span class="sxs-lookup"><span data-stu-id="95efd-145">ApiController attribute</span></span>

<span data-ttu-id="95efd-146">[[Objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut lze použít pro třídu kontroleru k povolení chování pro konkrétní rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="95efd-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable API-specific behaviors:</span></span>

* [<span data-ttu-id="95efd-147">Atribut směrování požadavků</span><span class="sxs-lookup"><span data-stu-id="95efd-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="95efd-148">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="95efd-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="95efd-149">Odvození parametr zdroje vazby</span><span class="sxs-lookup"><span data-stu-id="95efd-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="95efd-150">Odvození multipart/formulář data požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="95efd-151">Podrobnosti o problému pro stavové kódy chyb</span><span class="sxs-lookup"><span data-stu-id="95efd-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="95efd-152">Tyto funkce vyžadují [verze kompatibility](<xref:mvc/compatibility-version>) 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="95efd-152">These features require a [compatibility version](<xref:mvc/compatibility-version>) of 2.1 or later.</span></span>

### <a name="apicontroller-on-specific-controllers"></a><span data-ttu-id="95efd-153">Objektu ApiController na konkrétní řadiče</span><span class="sxs-lookup"><span data-stu-id="95efd-153">ApiController on specific controllers</span></span>

<span data-ttu-id="95efd-154">`[ApiController]` Atribut lze použít pro konkrétní řadiče, jako v následujícím příkladu ze šablony projektu:</span><span class="sxs-lookup"><span data-stu-id="95efd-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a><span data-ttu-id="95efd-155">Objektu ApiController na víc řadičích</span><span class="sxs-lookup"><span data-stu-id="95efd-155">ApiController on multiple controllers</span></span>

<span data-ttu-id="95efd-156">Jeden ze způsobů použití atributu na více než jeden řadič, je vytvořit třídu vlastní základní kontroler opatřen poznámkou `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="95efd-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="95efd-157">Tady je příklad ukazující vlastní základní třídu a kontroler, který je odvozen z něj:</span><span class="sxs-lookup"><span data-stu-id="95efd-157">Here's an example showing a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a><span data-ttu-id="95efd-158">Objektu ApiController na sestavení</span><span class="sxs-lookup"><span data-stu-id="95efd-158">ApiController on an assembly</span></span>

<span data-ttu-id="95efd-159">Pokud [verze kompatibility](<xref:mvc/compatibility-version>) je nastavená na verzi 2.2 nebo novější, `[ApiController]` atribut lze použít k sestavení.</span><span class="sxs-lookup"><span data-stu-id="95efd-159">If [compatibility version](<xref:mvc/compatibility-version>) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="95efd-160">Poznámka tímto způsobem použít chování webového rozhraní API do všech řadičů v sestavení.</span><span class="sxs-lookup"><span data-stu-id="95efd-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="95efd-161">Neexistuje žádný způsob, jak se odhlásit pro jednotlivé řadiče.</span><span class="sxs-lookup"><span data-stu-id="95efd-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="95efd-162">Atribut úrovně sestavení, který chcete použít `Startup` třídy, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="95efd-162">Apply the assembly-level attribute to the `Startup` class as shown in the following example:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

## <a name="attribute-routing-requirement"></a><span data-ttu-id="95efd-163">Atribut směrování požadavků</span><span class="sxs-lookup"><span data-stu-id="95efd-163">Attribute routing requirement</span></span>

<span data-ttu-id="95efd-164">`ApiController` Atribut díky směrování požadavku atributů.</span><span class="sxs-lookup"><span data-stu-id="95efd-164">The `ApiController` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="95efd-165">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95efd-165">For example:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

<span data-ttu-id="95efd-166">Akce jsou nedostupné přes [trasy konvenční](xref:mvc/controllers/routing#conventional-routing) určené <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="95efd-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

## <a name="automatic-http-400-responses"></a><span data-ttu-id="95efd-167">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="95efd-167">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="95efd-168">`ApiController` Atribut umožňuje ověření chyby modelu automaticky odpovědi na trigger HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="95efd-168">The `ApiController` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="95efd-169">V důsledku toho není nutné v metodě akce následující kód:</span><span class="sxs-lookup"><span data-stu-id="95efd-169">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a><span data-ttu-id="95efd-170">Výchozí odpověď chybného požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-170">Default BadRequest response</span></span> 

<span data-ttu-id="95efd-171">Kompatibilita verzí 2.2 nebo novější, je výchozí typ odpovědi pro odpovědi HTTP 400 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="95efd-171">With a compatibility version of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="95efd-172">`ValidationProblemDetails` Typ v souladu s [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="95efd-172">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="95efd-173">Chcete-li změnit výchozí odpověď <xref:Microsoft.AspNetCore.Mvc.SerializableError>, nastavte `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` vlastnost `true` v `Startup.ConfigureServices`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="95efd-173">To change the default response to <xref:Microsoft.AspNetCore.Mvc.SerializableError>, set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a><span data-ttu-id="95efd-174">Přizpůsobení chybného požadavku odpovědi</span><span class="sxs-lookup"><span data-stu-id="95efd-174">Customize BadRequest response</span></span>

<span data-ttu-id="95efd-175">Chcete-li přizpůsobit odpověď, která je výsledkem chyba ověřování, použijte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span><span class="sxs-lookup"><span data-stu-id="95efd-175">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="95efd-176">Přidejte následující zvýrazněný kód po `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="95efd-176">Add the following highlighted code after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="disable-automatic-400"></a><span data-ttu-id="95efd-177">Zakázat automatické 400</span><span class="sxs-lookup"><span data-stu-id="95efd-177">Disable automatic 400</span></span>

<span data-ttu-id="95efd-178">Chcete-li zakázat automatické 400 chování, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="95efd-178">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="95efd-179">Přidejte následující zvýrazněný kód v `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="95efd-179">Add the following highlighted code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="95efd-180">Odvození parametr zdroje vazby</span><span class="sxs-lookup"><span data-stu-id="95efd-180">Binding source parameter inference</span></span>

<span data-ttu-id="95efd-181">Zdrojový atribut vazby definuje umístění, ve kterém není nalezena hodnota parametru akce.</span><span class="sxs-lookup"><span data-stu-id="95efd-181">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="95efd-182">Existují následující atributy zdroje vazby:</span><span class="sxs-lookup"><span data-stu-id="95efd-182">The following binding source attributes exist:</span></span>

|<span data-ttu-id="95efd-183">Atribut</span><span class="sxs-lookup"><span data-stu-id="95efd-183">Attribute</span></span>|<span data-ttu-id="95efd-184">Zdroje připojení</span><span class="sxs-lookup"><span data-stu-id="95efd-184">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="95efd-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="95efd-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="95efd-186">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-186">Request body</span></span> |
|<span data-ttu-id="95efd-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="95efd-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="95efd-188">Data formuláře v textu požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-188">Form data in the request body</span></span> |
|<span data-ttu-id="95efd-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="95efd-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="95efd-190">Hlavička požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-190">Request header</span></span> |
|<span data-ttu-id="95efd-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="95efd-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="95efd-192">Požádat o parametru řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="95efd-192">Request query string parameter</span></span> |
|<span data-ttu-id="95efd-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="95efd-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="95efd-194">Data trasy z aktuální žádosti</span><span class="sxs-lookup"><span data-stu-id="95efd-194">Route data from the current request</span></span> |
|<span data-ttu-id="95efd-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="95efd-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="95efd-196">Žádost o služby, vložený jako parametru akce</span><span class="sxs-lookup"><span data-stu-id="95efd-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="95efd-197">Nepoužívejte `[FromRoute]` při může obsahovat hodnoty `%2f` (to znamená `/`).</span><span class="sxs-lookup"><span data-stu-id="95efd-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="95efd-198">`%2f` nebude znaků bez řídících k `/`.</span><span class="sxs-lookup"><span data-stu-id="95efd-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="95efd-199">Použití `[FromQuery]` Pokud hodnota může obsahovat `%2f`.</span><span class="sxs-lookup"><span data-stu-id="95efd-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="95efd-200">Bez `[ApiController]` vazbu atribut nebo atributy zdroje, jako jsou `[FromQuery]`, modul runtime ASP.NET Core se pokusí použít komplexní objekt vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="95efd-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="95efd-201">Vazač modelu komplexní objekt si vyžádá data z zprostředkovatele hodnot v zadaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="95efd-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="95efd-202">V následujícím příkladu `[FromQuery]` atribut označuje, že `discontinuedOnly` je zadána hodnota parametru v řetězci dotazu v adrese URL požadavku:</span><span class="sxs-lookup"><span data-stu-id="95efd-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="95efd-203">`[ApiController]` Atributu, platí pravidla odvozování pro zdroje dat výchozí parametry akce.</span><span class="sxs-lookup"><span data-stu-id="95efd-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="95efd-204">Tato pravidla můžete uložit z museli ručně určení zdroje připojení použitím atributy k parametrům akce.</span><span class="sxs-lookup"><span data-stu-id="95efd-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="95efd-205">Vazba zdrojové odvození, kterou pravidla chování následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="95efd-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="95efd-206">`[FromBody]` pro parametry komplexní typ je odvozen.</span><span class="sxs-lookup"><span data-stu-id="95efd-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="95efd-207">Výjimkou `[FromBody]` odvozené pravidlo je libovolný integrované, komplexní typ zvláštní význam, jako například <xref:Microsoft.AspNetCore.Http.IFormCollection> a <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="95efd-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="95efd-208">Zdrojový kód odvození vazby ignoruje tyto speciální typy.</span><span class="sxs-lookup"><span data-stu-id="95efd-208">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="95efd-209">`[FromForm]` pro parametry akce typu je odvozený <xref:Microsoft.AspNetCore.Http.IFormFile> a <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="95efd-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="95efd-210">Není odvodit pro jednoduché nebo uživatelem definované typy.</span><span class="sxs-lookup"><span data-stu-id="95efd-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="95efd-211">`[FromRoute]` je odvozen pro název parametru žádné akce odpovídající parametr v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="95efd-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="95efd-212">Když víc tras odpovídá parametru akce, je považován za libovolnou hodnotu trasy `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="95efd-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="95efd-213">`[FromQuery]` je odvozen pro všechny ostatní parametry akce.</span><span class="sxs-lookup"><span data-stu-id="95efd-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="95efd-214">Poznámky k odvození FromBody</span><span class="sxs-lookup"><span data-stu-id="95efd-214">FromBody inference notes</span></span>

<span data-ttu-id="95efd-215">`[FromBody]` není odvodit pro jednoduché typy, jako například `string` nebo `int`.</span><span class="sxs-lookup"><span data-stu-id="95efd-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="95efd-216">Proto `[FromBody]` atribut by měl použít pro jednoduché typy, které tuto funkci je potřeba.</span><span class="sxs-lookup"><span data-stu-id="95efd-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="95efd-217">Když akce má více než jeden parametr vázán z textu požadavku, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="95efd-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="95efd-218">Například všechny následující podpisy metod akce způsobit výjimku:</span><span class="sxs-lookup"><span data-stu-id="95efd-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="95efd-219">`[FromBody]` u obou odvodit, protože jsou komplexní typy.</span><span class="sxs-lookup"><span data-stu-id="95efd-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="95efd-220">`[FromBody]` atribut na jednom, protože se jedná o komplexní typ odvodit na druhé.</span><span class="sxs-lookup"><span data-stu-id="95efd-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="95efd-221">`[FromBody]` atribut u obou.</span><span class="sxs-lookup"><span data-stu-id="95efd-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> <span data-ttu-id="95efd-222">V ASP.NET Core 2.1 parametry typu kolekce, jako je například seznamy a pole jsou odvozeny nesprávně jako `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="95efd-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="95efd-223">`[FromBody]` Atributu se použije pro tyto parametry, pokud mají být vázány z textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="95efd-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="95efd-224">Toto chování je opraveno v ASP.NET Core 2.2 nebo vyšší, ve kterém jsou parametry typu kolekce odvodit vázat z textu ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="95efd-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

### <a name="disable-inference-rules"></a><span data-ttu-id="95efd-225">Zakázat odvozených pravidel</span><span class="sxs-lookup"><span data-stu-id="95efd-225">Disable inference rules</span></span>

<span data-ttu-id="95efd-226">Chcete-li zakázat odvození zdroj vazby, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> k `true`.</span><span class="sxs-lookup"><span data-stu-id="95efd-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="95efd-227">Přidejte následující kód do `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion`:</span><span class="sxs-lookup"><span data-stu-id="95efd-227">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="95efd-228">Odvození multipart/formulář data požadavku</span><span class="sxs-lookup"><span data-stu-id="95efd-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="95efd-229">`[ApiController]` Atributu platí odvozené pravidlo, pokud parametr akce je opatřen poznámkou [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atribut: `multipart/form-data` žádosti je odvozený typ obsahu.</span><span class="sxs-lookup"><span data-stu-id="95efd-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute: the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="95efd-230">Chcete-li výchozí chování zakázat, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> k `true` v `Startup.ConfigureServices`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="95efd-230">To disable the default behavior, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>  to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="95efd-231">Podrobnosti o problému pro stavové kódy chyb</span><span class="sxs-lookup"><span data-stu-id="95efd-231">Problem details for error status codes</span></span>

<span data-ttu-id="95efd-232">Pokud je kompatibility verzí 2.2 nebo vyšší, MVC transformuje chybného výsledku (výsledek s stavový kód 400 nebo vyšší) k výsledku s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="95efd-232">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="95efd-233">`ProblemDetails` Typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobnosti o chybě Strojově čitelný v odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="95efd-233">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="95efd-234">Vezměte v úvahu následující kód do kontroleru akce:</span><span class="sxs-lookup"><span data-stu-id="95efd-234">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="95efd-235">Odpověď HTTP pro `NotFound` má stavový kód 404 s `ProblemDetails` textu.</span><span class="sxs-lookup"><span data-stu-id="95efd-235">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="95efd-236">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95efd-236">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="95efd-237">Přizpůsobení ProblemDetails odpovědi</span><span class="sxs-lookup"><span data-stu-id="95efd-237">Customize ProblemDetails response</span></span>

<span data-ttu-id="95efd-238">Použití `ClientErrorMapping` vlastnosti ke konfiguraci obsah `ProblemDetails` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="95efd-238">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="95efd-239">Například následující kód aktualizace `type` vlastnost obdržíte kód odpovědi 404:</span><span class="sxs-lookup"><span data-stu-id="95efd-239">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a><span data-ttu-id="95efd-240">Zakázat ProblemDetails odezvy</span><span class="sxs-lookup"><span data-stu-id="95efd-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="95efd-241">Automatická tvorba `ProblemDetails` vypnutá při `SuppressMapClientErrors` je nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="95efd-241">The automatic creation of `ProblemDetails` is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="95efd-242">Přidejte následující kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="95efd-242">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a><span data-ttu-id="95efd-243">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="95efd-243">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
