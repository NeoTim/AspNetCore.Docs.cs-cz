---
title: Vytváření webových rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Seznamte se se základy vytváření webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 01/27/2020
uid: web-api/index
ms.openlocfilehash: 8609e2095c202643cdc905cc610298195b654215
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870014"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="ae1de-103">Vytváření webových rozhraní API pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae1de-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="ae1de-104">[Scott Addie](https://github.com/scottaddie) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ae1de-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ae1de-105">ASP.NET Core podporuje vytváření služeb RESTful, označovaných také jako webová rozhraní API C#, pomocí.</span><span class="sxs-lookup"><span data-stu-id="ae1de-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="ae1de-106">Webové rozhraní API zpracovává žádosti pomocí řadičů.</span><span class="sxs-lookup"><span data-stu-id="ae1de-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="ae1de-107">*Řadiče* ve webovém rozhraní API jsou třídy, které jsou odvozeny z `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="ae1de-108">V tomto článku se dozvíte, jak používat řadiče pro zpracování požadavků webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ae1de-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="ae1de-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="ae1de-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="ae1de-110">([Stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ae1de-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="ae1de-111">ControllerBase – třída</span><span class="sxs-lookup"><span data-stu-id="ae1de-111">ControllerBase class</span></span>

<span data-ttu-id="ae1de-112">Webové rozhraní API se skládá z jedné nebo více tříd kontroleru odvozených z <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="ae1de-113">Šablona projektu webového rozhraní API poskytuje kontroler Starter:</span><span class="sxs-lookup"><span data-stu-id="ae1de-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="ae1de-114">Nevytvářejte kontroler webového rozhraní API odvozením z třídy <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="ae1de-115">`Controller` se odvozují z `ControllerBase` a přidává podporu pro zobrazení, takže je možné zpracovávat webové stránky, nikoli požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ae1de-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="ae1de-116">Toto pravidlo má výjimku: Pokud plánujete použít stejný kontroler pro obě zobrazení a webová rozhraní API, odvozuje je od `Controller`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="ae1de-117">Třída `ControllerBase` poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae1de-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="ae1de-118">`ControllerBase.CreatedAtAction` například vrátí stavový kód 201:</span><span class="sxs-lookup"><span data-stu-id="ae1de-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="ae1de-119">Zde je několik příkladů metod, které `ControllerBase` poskytuje.</span><span class="sxs-lookup"><span data-stu-id="ae1de-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="ae1de-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="ae1de-120">Method</span></span>   |<span data-ttu-id="ae1de-121">Poznámky</span><span class="sxs-lookup"><span data-stu-id="ae1de-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="ae1de-122">Vrátí stavový kód 400.</span><span class="sxs-lookup"><span data-stu-id="ae1de-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="ae1de-123">Vrátí stavový kód 404.</span><span class="sxs-lookup"><span data-stu-id="ae1de-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="ae1de-124">Vrátí soubor.</span><span class="sxs-lookup"><span data-stu-id="ae1de-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="ae1de-125">Vyvolá [vazbu modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="ae1de-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="ae1de-126">Vyvolá [ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="ae1de-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="ae1de-127">Seznam všech dostupných metod a vlastností naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="ae1de-128">Atributy</span><span class="sxs-lookup"><span data-stu-id="ae1de-128">Attributes</span></span>

<span data-ttu-id="ae1de-129">Obor názvů <xref:Microsoft.AspNetCore.Mvc> poskytuje atributy, které lze použít ke konfiguraci chování řadičů webového rozhraní API a metod akcí.</span><span class="sxs-lookup"><span data-stu-id="ae1de-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="ae1de-130">Následující příklad používá atributy k určení podporovaného příkazu akce HTTP a všech známých stavových kódů HTTP, které by mohly být vráceny:</span><span class="sxs-lookup"><span data-stu-id="ae1de-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="ae1de-131">Tady je několik příkladů atributů, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="ae1de-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="ae1de-132">Atribut</span><span class="sxs-lookup"><span data-stu-id="ae1de-132">Attribute</span></span>|<span data-ttu-id="ae1de-133">Poznámky</span><span class="sxs-lookup"><span data-stu-id="ae1de-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="ae1de-134">Určuje vzor adresy URL pro kontroler nebo akci.</span><span class="sxs-lookup"><span data-stu-id="ae1de-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="ae1de-135">Určuje předponu a vlastnosti, které se mají zahrnout do vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="ae1de-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="ae1de-136">Identifikuje akci, která podporuje příkaz akce HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="ae1de-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="ae1de-137">Určuje datové typy, které akce akceptuje.</span><span class="sxs-lookup"><span data-stu-id="ae1de-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="ae1de-138">Určuje datové typy, které akce vrátí.</span><span class="sxs-lookup"><span data-stu-id="ae1de-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="ae1de-139">Seznam, který obsahuje dostupné atributy, najdete v oboru názvů <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="ae1de-140">ApiController – atribut</span><span class="sxs-lookup"><span data-stu-id="ae1de-140">ApiController attribute</span></span>

<span data-ttu-id="ae1de-141">Atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) lze použít pro třídu kontroleru, aby bylo možné povolit následující dogmatickým chování specifické pro rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="ae1de-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="ae1de-142">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="ae1de-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="ae1de-143">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="ae1de-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="ae1de-144">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="ae1de-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="ae1de-145">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="ae1de-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="ae1de-146">Podrobnosti o problémech stavových kódů chyb</span><span class="sxs-lookup"><span data-stu-id="ae1de-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="ae1de-147">*Podrobnosti o problému pro stavové kódy chyb* vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ae1de-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="ae1de-148">Ostatní funkce vyžadují verzi kompatibility 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ae1de-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="ae1de-149">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="ae1de-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="ae1de-150">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="ae1de-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="ae1de-151">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="ae1de-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="ae1de-152">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="ae1de-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="ae1de-153">Tyto funkce vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ae1de-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="ae1de-154">Atribut na určitých řadičích</span><span class="sxs-lookup"><span data-stu-id="ae1de-154">Attribute on specific controllers</span></span>

<span data-ttu-id="ae1de-155">Atribut `[ApiController]` lze použít pro konkrétní řadiče, jako v následujícím příkladu, ze šablony projektu:</span><span class="sxs-lookup"><span data-stu-id="ae1de-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="ae1de-156">Atribut na více řadičích</span><span class="sxs-lookup"><span data-stu-id="ae1de-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="ae1de-157">Jedním z přístupů k použití atributu na více než jednom řadiči je vytvoření vlastní třídy základního kontroleru s poznámkou s atributem `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="ae1de-158">Následující příklad ukazuje vlastní základní třídu a řadič, který je z něj odvozen:</span><span class="sxs-lookup"><span data-stu-id="ae1de-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="ae1de-159">Atribut na sestavení</span><span class="sxs-lookup"><span data-stu-id="ae1de-159">Attribute on an assembly</span></span>

<span data-ttu-id="ae1de-160">Je-li [verze kompatibility](xref:mvc/compatibility-version) nastavena na 2,2 nebo vyšší, lze atribut `[ApiController]` použít na sestavení.</span><span class="sxs-lookup"><span data-stu-id="ae1de-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="ae1de-161">Anotace tímto způsobem aplikuje chování webového rozhraní API na všechny řadiče v sestavení.</span><span class="sxs-lookup"><span data-stu-id="ae1de-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="ae1de-162">Neexistuje žádný způsob, jak odhlásit jednotlivé řadiče.</span><span class="sxs-lookup"><span data-stu-id="ae1de-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="ae1de-163">Použijte atribut na úrovni sestavení pro deklaraci oboru názvů obklopující třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ae1de-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="ae1de-164">Požadavek na směrování atributu</span><span class="sxs-lookup"><span data-stu-id="ae1de-164">Attribute routing requirement</span></span>

<span data-ttu-id="ae1de-165">Atribut `[ApiController]` dělá směrování požadavku.</span><span class="sxs-lookup"><span data-stu-id="ae1de-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="ae1de-166">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ae1de-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="ae1de-167">Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="ae1de-168">Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="ae1de-169">Automatické odpovědi HTTP 400</span><span class="sxs-lookup"><span data-stu-id="ae1de-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="ae1de-170">Atribut `[ApiController]` způsobuje, že chyby ověření modelu automaticky aktivují odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="ae1de-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="ae1de-171">V důsledku toho následující kód není zbytečný v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="ae1de-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="ae1de-172">ASP.NET Core MVC používá filtr akcí <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> k provedení předchozí kontroly.</span><span class="sxs-lookup"><span data-stu-id="ae1de-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="ae1de-173">Výchozí odpověď důvodu chybného požadavku</span><span class="sxs-lookup"><span data-stu-id="ae1de-173">Default BadRequest response</span></span>

<span data-ttu-id="ae1de-174">V případě kompatibility verze 2,1 je výchozí typ odpovědi pro odpověď HTTP 400 <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="ae1de-175">Následující text žádosti je příkladem serializovaného typu:</span><span class="sxs-lookup"><span data-stu-id="ae1de-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ae1de-176">V případě kompatibility verze 2,2 nebo novější je <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>výchozí typ odpovědi pro odpověď HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="ae1de-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="ae1de-177">Následující text žádosti je příkladem serializovaného typu:</span><span class="sxs-lookup"><span data-stu-id="ae1de-177">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="ae1de-178">Typ `ValidationProblemDetails`:</span><span class="sxs-lookup"><span data-stu-id="ae1de-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="ae1de-179">Poskytuje strojově čitelný formát pro zadávání chyb v odpovědích webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ae1de-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="ae1de-180">Vyhovuje [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="ae1de-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="ae1de-181">Protokolovat automatické odpovědi 400</span><span class="sxs-lookup"><span data-stu-id="ae1de-181">Log automatic 400 responses</span></span>

<span data-ttu-id="ae1de-182">Přečtěte si, [Jak protokolovat automatické odpovědi 400 na chyby ověření modelu (ASPNET/AspNetCore. Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="ae1de-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="ae1de-183">Zakázat automatickou odpověď 400</span><span class="sxs-lookup"><span data-stu-id="ae1de-183">Disable automatic 400 response</span></span>

<span data-ttu-id="ae1de-184">Chcete-li zakázat automatické chování 400, nastavte vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="ae1de-185">Do `Startup.ConfigureServices`přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="ae1de-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="ae1de-186">Odvození zdrojového parametru vazby</span><span class="sxs-lookup"><span data-stu-id="ae1de-186">Binding source parameter inference</span></span>

<span data-ttu-id="ae1de-187">Zdrojový atribut vazby definuje umístění, kde je nalezena hodnota parametru akce.</span><span class="sxs-lookup"><span data-stu-id="ae1de-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="ae1de-188">Existují následující zdrojové atributy vazby:</span><span class="sxs-lookup"><span data-stu-id="ae1de-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="ae1de-189">Atribut</span><span class="sxs-lookup"><span data-stu-id="ae1de-189">Attribute</span></span>|<span data-ttu-id="ae1de-190">Zdroj vazby</span><span class="sxs-lookup"><span data-stu-id="ae1de-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="ae1de-191">Tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="ae1de-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="ae1de-192">Data formuláře v textu žádosti</span><span class="sxs-lookup"><span data-stu-id="ae1de-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="ae1de-193">Hlavička žádosti</span><span class="sxs-lookup"><span data-stu-id="ae1de-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="ae1de-194">Parametr řetězce dotazu žádosti</span><span class="sxs-lookup"><span data-stu-id="ae1de-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="ae1de-195">Směrovat data z aktuální žádosti</span><span class="sxs-lookup"><span data-stu-id="ae1de-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="ae1de-196">Služba požadavku byla vložena jako parametr akce.</span><span class="sxs-lookup"><span data-stu-id="ae1de-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="ae1de-197">Nepoužívejte `[FromRoute]`, pokud hodnoty mohou obsahovat `%2f` (`/`).</span><span class="sxs-lookup"><span data-stu-id="ae1de-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="ae1de-198">`%2f` nebude `/`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="ae1de-199">Použijte `[FromQuery]`, pokud hodnota může obsahovat `%2f`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="ae1de-200">Bez atributu `[ApiController]` nebo vazby zdrojových atributů jako `[FromQuery]`se ASP.NET Core modul runtime pokusí použít komplexní pořadač objektového modelu.</span><span class="sxs-lookup"><span data-stu-id="ae1de-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="ae1de-201">Pořadač komplexního objektového modelu získává data od zprostředkovatelů hodnot v definovaném pořadí.</span><span class="sxs-lookup"><span data-stu-id="ae1de-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="ae1de-202">V následujícím příkladu atribut `[FromQuery]` označuje, že hodnota parametru `discontinuedOnly` je uvedena v řetězci dotazu adresy URL požadavku:</span><span class="sxs-lookup"><span data-stu-id="ae1de-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="ae1de-203">Atribut `[ApiController]` aplikuje odvozená pravidla pro výchozí zdroje dat parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="ae1de-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="ae1de-204">Tato pravidla vám umožní určit ruční identifikaci zdrojů vazby použitím atributů u parametrů akce.</span><span class="sxs-lookup"><span data-stu-id="ae1de-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="ae1de-205">Pravidla odvození zdroje vazby se chovají následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="ae1de-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="ae1de-206">`[FromBody]` je odvozený pro parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="ae1de-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="ae1de-207">Výjimka pro pravidlo odvození `[FromBody]` je jakékoli komplexní, vestavěný typ se speciálním významem, jako je například <xref:Microsoft.AspNetCore.Http.IFormCollection> a <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="ae1de-208">Kód odvození zdroje vazby ignoruje tyto speciální typy.</span><span class="sxs-lookup"><span data-stu-id="ae1de-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="ae1de-209">`[FromForm]` je odvozeno od parametrů akce typu <xref:Microsoft.AspNetCore.Http.IFormFile> a <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="ae1de-210">Není odvozený pro žádné jednoduché nebo uživatelsky definované typy.</span><span class="sxs-lookup"><span data-stu-id="ae1de-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="ae1de-211">`[FromRoute]` se odvozuje pro všechny názvy parametrů akcí, které odpovídají parametru v šabloně směrování.</span><span class="sxs-lookup"><span data-stu-id="ae1de-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="ae1de-212">Pokud parametr akce odpovídá víc než jedna trasa, hodnota trasy se považuje za `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="ae1de-213">`[FromQuery]` se odvozuje pro všechny ostatní parametry akce.</span><span class="sxs-lookup"><span data-stu-id="ae1de-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="ae1de-214">Poznámky k odvozování FromBody</span><span class="sxs-lookup"><span data-stu-id="ae1de-214">FromBody inference notes</span></span>

<span data-ttu-id="ae1de-215">`[FromBody]` nejsou odvozené pro jednoduché typy, jako je `string` nebo `int`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="ae1de-216">Proto by měl atribut `[FromBody]` použít pro jednoduché typy, pokud je tato funkce potřebná.</span><span class="sxs-lookup"><span data-stu-id="ae1de-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="ae1de-217">Pokud má akce více než jeden parametr vázaný z těla požadavku, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="ae1de-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="ae1de-218">Například všechny následující signatury metody Action způsobují výjimku:</span><span class="sxs-lookup"><span data-stu-id="ae1de-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="ae1de-219">`[FromBody]` odvozená v obou případech, protože se jedná o komplexní typy.</span><span class="sxs-lookup"><span data-stu-id="ae1de-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="ae1de-220">`[FromBody]` atribut na jednom, odvozený na druhém, protože se jedná o komplexní typ.</span><span class="sxs-lookup"><span data-stu-id="ae1de-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="ae1de-221">atribut `[FromBody]` v obou.</span><span class="sxs-lookup"><span data-stu-id="ae1de-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="ae1de-222">V ASP.NET Core 2,1 jsou parametry typu kolekce, jako jsou seznamy a pole, nesprávně odvozeny jako `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="ae1de-223">Atribut `[FromBody]` by měl být použit pro tyto parametry, pokud mají být vázány z textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="ae1de-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="ae1de-224">Toto chování se opravuje v ASP.NET Core 2,2 nebo novějším, kde jsou parametry typu kolekce odvoditelné z těla ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="ae1de-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="ae1de-225">Zakázat odvození pravidel</span><span class="sxs-lookup"><span data-stu-id="ae1de-225">Disable inference rules</span></span>

<span data-ttu-id="ae1de-226">Chcete-li zakázat odvození zdroje vazby, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> na `true`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="ae1de-227">Do `Startup.ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="ae1de-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="ae1de-228">Multipart/form-odvození požadavku na data</span><span class="sxs-lookup"><span data-stu-id="ae1de-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="ae1de-229">Atribut `[ApiController]` aplikuje pravidlo odvození, pokud je parametr akce opatřen poznámkou s atributem [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) .</span><span class="sxs-lookup"><span data-stu-id="ae1de-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="ae1de-230">Je odvozený typ obsahu žádosti o `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="ae1de-231">Chcete-li zakázat výchozí chování, nastavte vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> na `true` v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ae1de-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

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

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="ae1de-232">Podrobnosti o problémech stavových kódů chyb</span><span class="sxs-lookup"><span data-stu-id="ae1de-232">Problem details for error status codes</span></span>

<span data-ttu-id="ae1de-233">Pokud je verze kompatibility 2,2 nebo novější, MVC transformuje výsledek chyby (výsledek s kódem stavu 400 nebo vyšší) na výsledek s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="ae1de-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="ae1de-234">`ProblemDetails` typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobností o chybě čitelné počítačem v odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ae1de-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="ae1de-235">V akci kontroleru zvažte následující kód:</span><span class="sxs-lookup"><span data-stu-id="ae1de-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="ae1de-236">Metoda `NotFound` generuje stavový kód HTTP 404 s `ProblemDetails` tělo.</span><span class="sxs-lookup"><span data-stu-id="ae1de-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="ae1de-237">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ae1de-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="ae1de-238">Zakázat odpověď ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="ae1de-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="ae1de-239">Automatické vytváření instance `ProblemDetails` je zakázáno, pokud je vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="ae1de-239">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="ae1de-240">Do `Startup.ConfigureServices`přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="ae1de-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ae1de-241">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="ae1de-241">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
