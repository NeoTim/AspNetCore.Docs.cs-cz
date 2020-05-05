---
title: Vytváření webových rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Seznamte se se základy vytváření webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 5ea82d36d305b1f6ba58fba1b4c5bb4dca22f912
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776523"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="4aa34-103">Vytváření webových rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4aa34-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="4aa34-104">[Scott Addie](https://github.com/scottaddie) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="4aa34-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="4aa34-105">ASP.NET Core podporuje vytváření služeb RESTful v jazyce C# (služby RESTful se označují také jako webová rozhraní API).</span><span class="sxs-lookup"><span data-stu-id="4aa34-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="4aa34-106">Webové rozhraní API zpracovává žádosti pomocí řadičů.</span><span class="sxs-lookup"><span data-stu-id="4aa34-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="4aa34-107">*Řadiče* ve webovém rozhraní API jsou třídy, které jsou `ControllerBase`odvozeny z.</span><span class="sxs-lookup"><span data-stu-id="4aa34-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="4aa34-108">V tomto článku se dozvíte, jak používat řadiče pro zpracování požadavků webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4aa34-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="4aa34-109">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="4aa34-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="4aa34-110">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4aa34-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="4aa34-111">ControllerBase – třída</span><span class="sxs-lookup"><span data-stu-id="4aa34-111">ControllerBase class</span></span>

<span data-ttu-id="4aa34-112">Webové rozhraní API se skládá z jedné nebo více tříd kontroleru, které <xref:Microsoft.AspNetCore.Mvc.ControllerBase>jsou odvozeny z.</span><span class="sxs-lookup"><span data-stu-id="4aa34-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="4aa34-113">Šablona projektu webového rozhraní API poskytuje kontroler Starter:</span><span class="sxs-lookup"><span data-stu-id="4aa34-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="4aa34-114">Nevytvářejte kontroler webového rozhraní API odvozením z <xref:Microsoft.AspNetCore.Mvc.Controller> třídy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="4aa34-115">`Controller`je odvozen z `ControllerBase` a přidává podporu pro zobrazení, aby bylo možné zpracovávat webové stránky, nikoli požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4aa34-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="4aa34-116">Toto pravidlo má výjimku: Pokud plánujete použít stejný kontroler pro obě zobrazení a webová rozhraní API, odvodit je od `Controller`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="4aa34-117">`ControllerBase` Třída poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aa34-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="4aa34-118">Například `ControllerBase.CreatedAtAction` vrátí stavový kód 201:</span><span class="sxs-lookup"><span data-stu-id="4aa34-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="4aa34-119">Zde je několik příkladů metod, které `ControllerBase` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="4aa34-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="4aa34-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="4aa34-120">Method</span></span>   |<span data-ttu-id="4aa34-121">Poznámky</span><span class="sxs-lookup"><span data-stu-id="4aa34-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="4aa34-122">Vrátí stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="4aa34-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="4aa34-123">Vrátí stavový kód 404.</span><span class="sxs-lookup"><span data-stu-id="4aa34-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="4aa34-124">Vrátí soubor.</span><span class="sxs-lookup"><span data-stu-id="4aa34-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="4aa34-125">Vyvolá [vazbu modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="4aa34-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="4aa34-126">Vyvolá [ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4aa34-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="4aa34-127">Seznam všech dostupných metod a vlastností naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="4aa34-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="4aa34-128">Atributy</span><span class="sxs-lookup"><span data-stu-id="4aa34-128">Attributes</span></span>

<span data-ttu-id="4aa34-129"><xref:Microsoft.AspNetCore.Mvc> Obor názvů poskytuje atributy, které lze použít ke konfiguraci chování řadičů webového rozhraní API a metod akcí.</span><span class="sxs-lookup"><span data-stu-id="4aa34-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="4aa34-130">Následující příklad používá atributy k určení podporovaného příkazu akce HTTP a všech známých stavových kódů HTTP, které by mohly být vráceny:</span><span class="sxs-lookup"><span data-stu-id="4aa34-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="4aa34-131">Tady je několik příkladů atributů, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4aa34-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="4aa34-132">Atribut</span><span class="sxs-lookup"><span data-stu-id="4aa34-132">Attribute</span></span>|<span data-ttu-id="4aa34-133">Poznámky</span><span class="sxs-lookup"><span data-stu-id="4aa34-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="4aa34-134">Určuje vzor adresy URL pro kontroler nebo akci.</span><span class="sxs-lookup"><span data-stu-id="4aa34-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="4aa34-135">Určuje předponu a vlastnosti, které se mají zahrnout do vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="4aa34-136">Identifikuje akci, která podporuje příkaz akce HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="4aa34-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="4aa34-137">Určuje datové typy, které akce akceptuje.</span><span class="sxs-lookup"><span data-stu-id="4aa34-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="4aa34-138">Určuje datové typy, které akce vrátí.</span><span class="sxs-lookup"><span data-stu-id="4aa34-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="4aa34-139">Seznam, který obsahuje dostupné atributy, najdete v tématu <xref:Microsoft.AspNetCore.Mvc> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="4aa34-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="4aa34-140">ApiController – atribut</span><span class="sxs-lookup"><span data-stu-id="4aa34-140">ApiController attribute</span></span>

<span data-ttu-id="4aa34-141">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Atribut lze použít pro třídu kontroleru a povolit následující dogmatickým chování specifické pro rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="4aa34-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="4aa34-142">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="4aa34-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="4aa34-143">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="4aa34-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="4aa34-144">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="4aa34-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="4aa34-145">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="4aa34-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="4aa34-146">Podrobnosti o problémech stavových kódů chyb</span><span class="sxs-lookup"><span data-stu-id="4aa34-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="4aa34-147">*Podrobnosti o problému pro stavové kódy chyb* vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="4aa34-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="4aa34-148">Ostatní funkce vyžadují verzi kompatibility 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="4aa34-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="4aa34-149">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="4aa34-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="4aa34-150">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="4aa34-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="4aa34-151">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="4aa34-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="4aa34-152">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="4aa34-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="4aa34-153">Tyto funkce vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="4aa34-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="4aa34-154">Atribut na určitých řadičích</span><span class="sxs-lookup"><span data-stu-id="4aa34-154">Attribute on specific controllers</span></span>

<span data-ttu-id="4aa34-155">`[ApiController]` Atribut lze použít pro konkrétní řadiče, jako v následujícím příkladu, ze šablony projektu:</span><span class="sxs-lookup"><span data-stu-id="4aa34-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="4aa34-156">Atribut na více řadičích</span><span class="sxs-lookup"><span data-stu-id="4aa34-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="4aa34-157">Jedním z přístupů k použití atributu na více než jednom řadiči je vytvoření vlastní třídy základního kontroleru s poznámkou s `[ApiController]` atributem.</span><span class="sxs-lookup"><span data-stu-id="4aa34-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="4aa34-158">Následující příklad ukazuje vlastní základní třídu a řadič, který je z něj odvozen:</span><span class="sxs-lookup"><span data-stu-id="4aa34-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="4aa34-159">Atribut na sestavení</span><span class="sxs-lookup"><span data-stu-id="4aa34-159">Attribute on an assembly</span></span>

<span data-ttu-id="4aa34-160">Pokud je [verze kompatibility](xref:mvc/compatibility-version) nastavena na 2,2 nebo novější, `[ApiController]` atribut lze použít na sestavení.</span><span class="sxs-lookup"><span data-stu-id="4aa34-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="4aa34-161">Anotace tímto způsobem aplikuje chování webového rozhraní API na všechny řadiče v sestavení.</span><span class="sxs-lookup"><span data-stu-id="4aa34-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="4aa34-162">Neexistuje žádný způsob, jak odhlásit jednotlivé řadiče.</span><span class="sxs-lookup"><span data-stu-id="4aa34-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="4aa34-163">Použijte atribut na úrovni sestavení pro deklaraci oboru názvů obklopující `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="4aa34-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="4aa34-164">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="4aa34-164">Attribute routing requirement</span></span>

<span data-ttu-id="4aa34-165">`[ApiController]` Atribut dělá směrování požadavku.</span><span class="sxs-lookup"><span data-stu-id="4aa34-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="4aa34-166">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4aa34-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="4aa34-167">Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných pomocí `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>nebo v.</span><span class="sxs-lookup"><span data-stu-id="4aa34-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="4aa34-168">Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> nebo.</span><span class="sxs-lookup"><span data-stu-id="4aa34-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="4aa34-169">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="4aa34-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="4aa34-170">`[ApiController]` Atribut způsobuje chyby ověření modelu automaticky a odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="4aa34-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="4aa34-171">V důsledku toho následující kód není zbytečný v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="4aa34-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="4aa34-172">ASP.NET Core MVC používá filtr <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> akcí k provedení předchozí kontroly.</span><span class="sxs-lookup"><span data-stu-id="4aa34-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="4aa34-173">Výchozí odpověď důvodu chybného požadavku</span><span class="sxs-lookup"><span data-stu-id="4aa34-173">Default BadRequest response</span></span>

<span data-ttu-id="4aa34-174">V případě kompatibility verze 2,1 je <xref:Microsoft.AspNetCore.Mvc.SerializableError>výchozí typ odpovědi pro odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="4aa34-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="4aa34-175">Následující text žádosti je příkladem serializovaného typu:</span><span class="sxs-lookup"><span data-stu-id="4aa34-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4aa34-176">V případě kompatibility verze 2,2 nebo novější je <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>výchozí typ odpovědi pro odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="4aa34-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="4aa34-177">Následující text žádosti je příkladem serializovaného typu:</span><span class="sxs-lookup"><span data-stu-id="4aa34-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="4aa34-178">`ValidationProblemDetails` Typ:</span><span class="sxs-lookup"><span data-stu-id="4aa34-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="4aa34-179">Poskytuje strojově čitelný formát pro zadávání chyb v odpovědích webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4aa34-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="4aa34-180">Vyhovuje [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="4aa34-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="4aa34-181">Protokolovat automatické odpovědi 400</span><span class="sxs-lookup"><span data-stu-id="4aa34-181">Log automatic 400 responses</span></span>

<span data-ttu-id="4aa34-182">Přečtěte si, [Jak protokolovat automatické odpovědi 400 na chyby ověření modelu (ASPNET/AspNetCore. Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="4aa34-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="4aa34-183">Zakázat automatickou odpověď 400</span><span class="sxs-lookup"><span data-stu-id="4aa34-183">Disable automatic 400 response</span></span>

<span data-ttu-id="4aa34-184">Chcete-li zakázat automatické chování 400, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> vlastnost na `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="4aa34-185">Do `Startup.ConfigureServices`následujícího pole přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="4aa34-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="4aa34-186">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="4aa34-186">Binding source parameter inference</span></span>

<span data-ttu-id="4aa34-187">Zdrojový atribut vazby definuje umístění, kde je nalezena hodnota parametru akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="4aa34-188">Existují následující zdrojové atributy vazby:</span><span class="sxs-lookup"><span data-stu-id="4aa34-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="4aa34-189">Atribut</span><span class="sxs-lookup"><span data-stu-id="4aa34-189">Attribute</span></span>|<span data-ttu-id="4aa34-190">Zdroj vazby</span><span class="sxs-lookup"><span data-stu-id="4aa34-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="4aa34-191">Text požadavku</span><span class="sxs-lookup"><span data-stu-id="4aa34-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="4aa34-192">Data formuláře v textu žádosti</span><span class="sxs-lookup"><span data-stu-id="4aa34-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="4aa34-193">Hlavička požadavku</span><span class="sxs-lookup"><span data-stu-id="4aa34-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="4aa34-194">Parametr řetězce dotazu žádosti</span><span class="sxs-lookup"><span data-stu-id="4aa34-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="4aa34-195">Směrovat data z aktuální žádosti</span><span class="sxs-lookup"><span data-stu-id="4aa34-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="4aa34-196">Služba požadavku byla vložena jako parametr akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="4aa34-197">Nepoužívejte `[FromRoute]` , pokud hodnoty mohou `%2f` obsahovat (to `/`znamená).</span><span class="sxs-lookup"><span data-stu-id="4aa34-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="4aa34-198">`%2f`nedá se odsekvencit na `/`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="4aa34-199">Použijte `[FromQuery]` , pokud hodnota může obsahovat `%2f`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="4aa34-200">`[ApiController]` Bez atributů nebo atributů vazby `[FromQuery]`, ASP.NET Core modul runtime se pokusí použít komplexní pořadač objektového modelu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="4aa34-201">Pořadač komplexního objektového modelu získává data od zprostředkovatelů hodnot v definovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="4aa34-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="4aa34-202">V následujícím příkladu `[FromQuery]` atribut označuje, že hodnota `discontinuedOnly` parametru je uvedena v řetězci dotazu adresy URL požadavku:</span><span class="sxs-lookup"><span data-stu-id="4aa34-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="4aa34-203">`[ApiController]` Atribut použije odvozená pravidla pro výchozí zdroje dat parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="4aa34-204">Tato pravidla vám umožní určit ruční identifikaci zdrojů vazby použitím atributů u parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="4aa34-205">Pravidla odvození zdroje vazby se chovají následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="4aa34-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="4aa34-206">`[FromBody]`je odvozený pro parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="4aa34-207">Výjimka na `[FromBody]` odvozený pravidlo je jakýkoli komplexní, vestavěný typ se speciálním významem, například <xref:Microsoft.AspNetCore.Http.IFormCollection> a. <xref:System.Threading.CancellationToken></span><span class="sxs-lookup"><span data-stu-id="4aa34-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="4aa34-208">Kód odvození zdroje vazby ignoruje tyto speciální typy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="4aa34-209">`[FromForm]`je odvozený pro parametry akce typu <xref:Microsoft.AspNetCore.Http.IFormFile> a. <xref:Microsoft.AspNetCore.Http.IFormFileCollection></span><span class="sxs-lookup"><span data-stu-id="4aa34-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="4aa34-210">Není odvozený pro žádné jednoduché nebo uživatelsky definované typy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="4aa34-211">`[FromRoute]`je odvozen pro všechny názvy parametrů akce, které odpovídají parametru v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="4aa34-212">Když parametr akce odpovídá víc než jedna trasa, bude se brát v úvahu `[FromRoute]`jakákoli hodnota trasy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="4aa34-213">`[FromQuery]`je odvozen pro všechny ostatní parametry akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="4aa34-214">Poznámky k odvozování FromBody</span><span class="sxs-lookup"><span data-stu-id="4aa34-214">FromBody inference notes</span></span>

<span data-ttu-id="4aa34-215">`[FromBody]`není odvozen pro jednoduché typy, jako například `string` nebo. `int`</span><span class="sxs-lookup"><span data-stu-id="4aa34-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="4aa34-216">Proto by měl `[FromBody]` být atribut použit pro jednoduché typy, pokud je tato funkce potřebná.</span><span class="sxs-lookup"><span data-stu-id="4aa34-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="4aa34-217">Pokud má akce více než jeden parametr vázaný z těla požadavku, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="4aa34-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="4aa34-218">Například všechny následující signatury metody Action způsobují výjimku:</span><span class="sxs-lookup"><span data-stu-id="4aa34-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="4aa34-219">`[FromBody]`odvozeno v obou případech, protože se jedná o komplexní typy.</span><span class="sxs-lookup"><span data-stu-id="4aa34-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="4aa34-220">`[FromBody]`atribut na jednom, odvozený na druhém, protože se jedná o komplexní typ.</span><span class="sxs-lookup"><span data-stu-id="4aa34-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="4aa34-221">`[FromBody]`atribut na obou.</span><span class="sxs-lookup"><span data-stu-id="4aa34-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="4aa34-222">V ASP.NET Core 2,1 jsou parametry typu kolekce, jako jsou seznamy a pole, nesprávně odvozeny jako `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="4aa34-223">`[FromBody]` Atribut by měl být použit pro tyto parametry, pokud mají být vázány z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="4aa34-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="4aa34-224">Toto chování se opravuje v ASP.NET Core 2,2 nebo novějším, kde jsou parametry typu kolekce odvoditelné z těla ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="4aa34-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="4aa34-225">Zakázat odvození pravidel</span><span class="sxs-lookup"><span data-stu-id="4aa34-225">Disable inference rules</span></span>

<span data-ttu-id="4aa34-226">Chcete-li zakázat odvození zdroje vazby <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> , `true`nastavte na.</span><span class="sxs-lookup"><span data-stu-id="4aa34-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="4aa34-227">Do `Startup.ConfigureServices`tohoto pole přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4aa34-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="4aa34-228">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="4aa34-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="4aa34-229">`[ApiController]` Atribut aplikuje odvození pravidla, pokud je parametr akce opatřen poznámkou s [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="4aa34-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="4aa34-230">Typ `multipart/form-data` obsahu žádosti je odvozený.</span><span class="sxs-lookup"><span data-stu-id="4aa34-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="4aa34-231">Chcete-li zakázat výchozí chování, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> vlastnost na `true` hodnotu `Startup.ConfigureServices`v:</span><span class="sxs-lookup"><span data-stu-id="4aa34-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="4aa34-232">Podrobnosti o problémech stavových kódů chyb</span><span class="sxs-lookup"><span data-stu-id="4aa34-232">Problem details for error status codes</span></span>

<span data-ttu-id="4aa34-233">Pokud je verze kompatibility 2,2 nebo novější, MVC transformuje výsledek chyby (výsledek s kódem stavu 400 nebo vyšší) na výsledek <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="4aa34-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="4aa34-234">`ProblemDetails` Typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobností o chybě ČITELNÉ počítačem v odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="4aa34-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="4aa34-235">V akci kontroleru zvažte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4aa34-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="4aa34-236">`NotFound` Metoda vytvoří stavový kód HTTP 404 s `ProblemDetails` tělem.</span><span class="sxs-lookup"><span data-stu-id="4aa34-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="4aa34-237">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4aa34-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="4aa34-238">Zakázat odpověď ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="4aa34-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="4aa34-239">Automatické vytváření stavových kódů `ProblemDetails` pro chyby je zakázáno, pokud je <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> vlastnost nastavena na `true`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="4aa34-240">Do `Startup.ConfigureServices`tohoto pole přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="4aa34-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="4aa34-241">Definujte podporované typy obsahu požadavků pomocí atributu [resupporteds].</span><span class="sxs-lookup"><span data-stu-id="4aa34-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="4aa34-242">Ve výchozím nastavení akce podporuje všechny dostupné typy obsahu žádostí.</span><span class="sxs-lookup"><span data-stu-id="4aa34-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="4aa34-243">Například pokud je aplikace nakonfigurována tak, aby podporovala [vstupní formátovací](xref:mvc/models/model-binding#input-formatters)moduly JSON a XML, akce podporuje více typů obsahu, včetně `application/json` a. `application/xml`</span><span class="sxs-lookup"><span data-stu-id="4aa34-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="4aa34-244">Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků.</span><span class="sxs-lookup"><span data-stu-id="4aa34-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="4aa34-245">Použijte `[Consumes]` atribut pro akci nebo kontroler a určete jeden nebo více typů obsahu:</span><span class="sxs-lookup"><span data-stu-id="4aa34-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="4aa34-246">V předchozím kódu `CreateProduct` akce určuje typ `application/xml`obsahu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="4aa34-247">Požadavky směrované na tuto akci musí určovat `Content-Type` hlavičku. `application/xml`</span><span class="sxs-lookup"><span data-stu-id="4aa34-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="4aa34-248">Požadavky, které nespecifikují `Content-Type` hlavičku `application/xml` , mají za následek [nepodporovanou odpověď typu média 415](https://developer.mozilla.org/docs/Web/HTTP/Status/415) .</span><span class="sxs-lookup"><span data-stu-id="4aa34-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="4aa34-249">`[Consumes]` Atribut také umožňuje, aby akce ovlivnila svůj výběr na základě typu obsahu příchozího požadavku, a to použitím omezení typu.</span><span class="sxs-lookup"><span data-stu-id="4aa34-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="4aa34-250">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="4aa34-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="4aa34-251">V předchozím kódu `ConsumesController` je nakonfigurováno pro zpracování požadavků odesílaných na `https://localhost:5001/api/Consumes` adresu URL.</span><span class="sxs-lookup"><span data-stu-id="4aa34-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="4aa34-252">Obě akce kontroleru `PostJson` a `PostForm`zpracovávají požadavky post se stejnou adresou URL.</span><span class="sxs-lookup"><span data-stu-id="4aa34-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="4aa34-253">Bez `[Consumes]` atributu, který používá omezení typu, je vyvolána výjimka dvojznačné shody.</span><span class="sxs-lookup"><span data-stu-id="4aa34-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="4aa34-254">`[Consumes]` Atribut se aplikuje na obě akce.</span><span class="sxs-lookup"><span data-stu-id="4aa34-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="4aa34-255">`PostJson` Akce zpracovává požadavky odeslané s `Content-Type` hlavičkou `application/json`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="4aa34-256">`PostForm` Akce zpracovává požadavky odeslané s `Content-Type` hlavičkou `application/x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="4aa34-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="4aa34-257">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4aa34-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
