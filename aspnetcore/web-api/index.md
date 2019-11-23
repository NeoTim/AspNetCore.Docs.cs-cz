---
title: Create web APIs with ASP.NET Core
author: scottaddie
description: Learn the basics of creating a web API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/22/2019
uid: web-api/index
ms.openlocfilehash: 3f52e4ce2d26902324ab30e0bda7ed8a4942daa0
ms.sourcegitcommit: ddc813f0f1fb293861a01597532919945b0e7fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74412040"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="da033-103">Create web APIs with ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da033-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="da033-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="da033-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="da033-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span><span class="sxs-lookup"><span data-stu-id="da033-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="da033-106">To handle requests, a web API uses controllers.</span><span class="sxs-lookup"><span data-stu-id="da033-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="da033-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="da033-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="da033-108">This article shows how to use controllers for handling web API requests.</span><span class="sxs-lookup"><span data-stu-id="da033-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="da033-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="da033-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="da033-110">([How to download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da033-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="da033-111">ControllerBase class</span><span class="sxs-lookup"><span data-stu-id="da033-111">ControllerBase class</span></span>

<span data-ttu-id="da033-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="da033-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="da033-113">The web API project template provides a starter controller:</span><span class="sxs-lookup"><span data-stu-id="da033-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="da033-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span><span class="sxs-lookup"><span data-stu-id="da033-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="da033-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span><span class="sxs-lookup"><span data-stu-id="da033-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="da033-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span><span class="sxs-lookup"><span data-stu-id="da033-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="da033-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span><span class="sxs-lookup"><span data-stu-id="da033-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="da033-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span><span class="sxs-lookup"><span data-stu-id="da033-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="da033-119">Here are some more examples of methods that `ControllerBase` provides.</span><span class="sxs-lookup"><span data-stu-id="da033-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="da033-120">Metoda</span><span class="sxs-lookup"><span data-stu-id="da033-120">Method</span></span>   |<span data-ttu-id="da033-121">Poznámky</span><span class="sxs-lookup"><span data-stu-id="da033-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="da033-122">Returns 400 status code.</span><span class="sxs-lookup"><span data-stu-id="da033-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="da033-123">Returns 404 status code.</span><span class="sxs-lookup"><span data-stu-id="da033-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="da033-124">Returns a file.</span><span class="sxs-lookup"><span data-stu-id="da033-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="da033-125">Invokes [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="da033-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="da033-126">Invokes [model validation](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="da033-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="da033-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="da033-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="da033-128">Atributy</span><span class="sxs-lookup"><span data-stu-id="da033-128">Attributes</span></span>

<span data-ttu-id="da033-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span><span class="sxs-lookup"><span data-stu-id="da033-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="da033-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span><span class="sxs-lookup"><span data-stu-id="da033-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="da033-131">Here are some more examples of attributes that are available.</span><span class="sxs-lookup"><span data-stu-id="da033-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="da033-132">Atribut</span><span class="sxs-lookup"><span data-stu-id="da033-132">Attribute</span></span>|<span data-ttu-id="da033-133">Poznámky</span><span class="sxs-lookup"><span data-stu-id="da033-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="da033-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="da033-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="da033-135">Specifies URL pattern for a controller or action.</span><span class="sxs-lookup"><span data-stu-id="da033-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="da033-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="da033-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="da033-137">Specifies prefix and properties to include for model binding.</span><span class="sxs-lookup"><span data-stu-id="da033-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="da033-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="da033-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="da033-139">Identifies an action that supports the HTTP GET action verb.</span><span class="sxs-lookup"><span data-stu-id="da033-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="da033-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="da033-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="da033-141">Specifies data types that an action accepts.</span><span class="sxs-lookup"><span data-stu-id="da033-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="da033-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="da033-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="da033-143">Specifies data types that an action returns.</span><span class="sxs-lookup"><span data-stu-id="da033-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="da033-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span><span class="sxs-lookup"><span data-stu-id="da033-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="da033-145">ApiController attribute</span><span class="sxs-lookup"><span data-stu-id="da033-145">ApiController attribute</span></span>

<span data-ttu-id="da033-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span><span class="sxs-lookup"><span data-stu-id="da033-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="da033-147">Attribute routing requirement</span><span class="sxs-lookup"><span data-stu-id="da033-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="da033-148">Automatic HTTP 400 responses</span><span class="sxs-lookup"><span data-stu-id="da033-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="da033-149">Binding source parameter inference</span><span class="sxs-lookup"><span data-stu-id="da033-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="da033-150">Multipart/form-data request inference</span><span class="sxs-lookup"><span data-stu-id="da033-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="da033-151">Problem details for error status codes</span><span class="sxs-lookup"><span data-stu-id="da033-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="da033-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span><span class="sxs-lookup"><span data-stu-id="da033-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="da033-153">Attribute on specific controllers</span><span class="sxs-lookup"><span data-stu-id="da033-153">Attribute on specific controllers</span></span>

<span data-ttu-id="da033-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span><span class="sxs-lookup"><span data-stu-id="da033-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="da033-155">Attribute on multiple controllers</span><span class="sxs-lookup"><span data-stu-id="da033-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="da033-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span><span class="sxs-lookup"><span data-stu-id="da033-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="da033-157">The following example shows a custom base class and a controller that derives from it:</span><span class="sxs-lookup"><span data-stu-id="da033-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="da033-158">Attribute on an assembly</span><span class="sxs-lookup"><span data-stu-id="da033-158">Attribute on an assembly</span></span>

<span data-ttu-id="da033-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span><span class="sxs-lookup"><span data-stu-id="da033-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="da033-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span><span class="sxs-lookup"><span data-stu-id="da033-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="da033-161">There's no way to opt out for individual controllers.</span><span class="sxs-lookup"><span data-stu-id="da033-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="da033-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span><span class="sxs-lookup"><span data-stu-id="da033-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="da033-163">Attribute routing requirement</span><span class="sxs-lookup"><span data-stu-id="da033-163">Attribute routing requirement</span></span>

<span data-ttu-id="da033-164">The `[ApiController]` attribute makes attribute routing a requirement.</span><span class="sxs-lookup"><span data-stu-id="da033-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="da033-165">Příklad:</span><span class="sxs-lookup"><span data-stu-id="da033-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="da033-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="da033-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="da033-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="da033-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="da033-168">Automatic HTTP 400 responses</span><span class="sxs-lookup"><span data-stu-id="da033-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="da033-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span><span class="sxs-lookup"><span data-stu-id="da033-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="da033-170">Consequently, the following code is unnecessary in an action method:</span><span class="sxs-lookup"><span data-stu-id="da033-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="da033-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span><span class="sxs-lookup"><span data-stu-id="da033-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="da033-172">Default BadRequest response</span><span class="sxs-lookup"><span data-stu-id="da033-172">Default BadRequest response</span></span>

<span data-ttu-id="da033-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="da033-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="da033-174">The following request body is an example of the serialized type:</span><span class="sxs-lookup"><span data-stu-id="da033-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="da033-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="da033-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="da033-176">The following request body is an example of the serialized type:</span><span class="sxs-lookup"><span data-stu-id="da033-176">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="da033-177">The `ValidationProblemDetails` type:</span><span class="sxs-lookup"><span data-stu-id="da033-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="da033-178">Provides a machine-readable format for specifying errors in web API responses.</span><span class="sxs-lookup"><span data-stu-id="da033-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="da033-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="da033-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="da033-180">Log automatic 400 responses</span><span class="sxs-lookup"><span data-stu-id="da033-180">Log automatic 400 responses</span></span>

<span data-ttu-id="da033-181">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="da033-181">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="da033-182">Disable automatic 400 response</span><span class="sxs-lookup"><span data-stu-id="da033-182">Disable automatic 400 response</span></span>

<span data-ttu-id="da033-183">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span><span class="sxs-lookup"><span data-stu-id="da033-183">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="da033-184">Add the following highlighted code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="da033-184">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="da033-185">Binding source parameter inference</span><span class="sxs-lookup"><span data-stu-id="da033-185">Binding source parameter inference</span></span>

<span data-ttu-id="da033-186">A binding source attribute defines the location at which an action parameter's value is found.</span><span class="sxs-lookup"><span data-stu-id="da033-186">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="da033-187">The following binding source attributes exist:</span><span class="sxs-lookup"><span data-stu-id="da033-187">The following binding source attributes exist:</span></span>

|<span data-ttu-id="da033-188">Atribut</span><span class="sxs-lookup"><span data-stu-id="da033-188">Attribute</span></span>|<span data-ttu-id="da033-189">Binding source</span><span class="sxs-lookup"><span data-stu-id="da033-189">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="da033-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="da033-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="da033-191">Request body</span><span class="sxs-lookup"><span data-stu-id="da033-191">Request body</span></span> |
|<span data-ttu-id="da033-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="da033-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="da033-193">Form data in the request body</span><span class="sxs-lookup"><span data-stu-id="da033-193">Form data in the request body</span></span> |
|<span data-ttu-id="da033-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="da033-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="da033-195">Request header</span><span class="sxs-lookup"><span data-stu-id="da033-195">Request header</span></span> |
|<span data-ttu-id="da033-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="da033-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="da033-197">Request query string parameter</span><span class="sxs-lookup"><span data-stu-id="da033-197">Request query string parameter</span></span> |
|<span data-ttu-id="da033-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="da033-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="da033-199">Route data from the current request</span><span class="sxs-lookup"><span data-stu-id="da033-199">Route data from the current request</span></span> |
|<span data-ttu-id="da033-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="da033-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="da033-201">The request service injected as an action parameter</span><span class="sxs-lookup"><span data-stu-id="da033-201">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="da033-202">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span><span class="sxs-lookup"><span data-stu-id="da033-202">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="da033-203">`%2f` won't be unescaped to `/`.</span><span class="sxs-lookup"><span data-stu-id="da033-203">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="da033-204">Use `[FromQuery]` if the value might contain `%2f`.</span><span class="sxs-lookup"><span data-stu-id="da033-204">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="da033-205">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span><span class="sxs-lookup"><span data-stu-id="da033-205">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="da033-206">The complex object model binder pulls data from value providers in a defined order.</span><span class="sxs-lookup"><span data-stu-id="da033-206">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="da033-207">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span><span class="sxs-lookup"><span data-stu-id="da033-207">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="da033-208">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span><span class="sxs-lookup"><span data-stu-id="da033-208">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="da033-209">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span><span class="sxs-lookup"><span data-stu-id="da033-209">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="da033-210">The binding source inference rules behave as follows:</span><span class="sxs-lookup"><span data-stu-id="da033-210">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="da033-211">`[FromBody]` is inferred for complex type parameters.</span><span class="sxs-lookup"><span data-stu-id="da033-211">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="da033-212">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="da033-212">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="da033-213">The binding source inference code ignores those special types.</span><span class="sxs-lookup"><span data-stu-id="da033-213">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="da033-214">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="da033-214">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="da033-215">It's not inferred for any simple or user-defined types.</span><span class="sxs-lookup"><span data-stu-id="da033-215">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="da033-216">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span><span class="sxs-lookup"><span data-stu-id="da033-216">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="da033-217">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="da033-217">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="da033-218">`[FromQuery]` is inferred for any other action parameters.</span><span class="sxs-lookup"><span data-stu-id="da033-218">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="da033-219">FromBody inference notes</span><span class="sxs-lookup"><span data-stu-id="da033-219">FromBody inference notes</span></span>

<span data-ttu-id="da033-220">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span><span class="sxs-lookup"><span data-stu-id="da033-220">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="da033-221">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span><span class="sxs-lookup"><span data-stu-id="da033-221">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="da033-222">When an action has more than one parameter bound from the request body, an exception is thrown.</span><span class="sxs-lookup"><span data-stu-id="da033-222">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="da033-223">For example, all of the following action method signatures cause an exception:</span><span class="sxs-lookup"><span data-stu-id="da033-223">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="da033-224">`[FromBody]` inferred on both because they're complex types.</span><span class="sxs-lookup"><span data-stu-id="da033-224">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="da033-225">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span><span class="sxs-lookup"><span data-stu-id="da033-225">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="da033-226">`[FromBody]` attribute on both.</span><span class="sxs-lookup"><span data-stu-id="da033-226">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="da033-227">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="da033-227">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="da033-228">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span><span class="sxs-lookup"><span data-stu-id="da033-228">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="da033-229">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span><span class="sxs-lookup"><span data-stu-id="da033-229">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="da033-230">Disable inference rules</span><span class="sxs-lookup"><span data-stu-id="da033-230">Disable inference rules</span></span>

<span data-ttu-id="da033-231">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span><span class="sxs-lookup"><span data-stu-id="da033-231">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="da033-232">Add the following code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="da033-232">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="da033-233">Multipart/form-data request inference</span><span class="sxs-lookup"><span data-stu-id="da033-233">Multipart/form-data request inference</span></span>

<span data-ttu-id="da033-234">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="da033-234">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="da033-235">The `multipart/form-data` request content type is inferred.</span><span class="sxs-lookup"><span data-stu-id="da033-235">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="da033-236">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="da033-236">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

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

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="da033-237">Problem details for error status codes</span><span class="sxs-lookup"><span data-stu-id="da033-237">Problem details for error status codes</span></span>

<span data-ttu-id="da033-238">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="da033-238">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="da033-239">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span><span class="sxs-lookup"><span data-stu-id="da033-239">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="da033-240">Consider the following code in a controller action:</span><span class="sxs-lookup"><span data-stu-id="da033-240">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="da033-241">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span><span class="sxs-lookup"><span data-stu-id="da033-241">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="da033-242">Příklad:</span><span class="sxs-lookup"><span data-stu-id="da033-242">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="da033-243">Disable ProblemDetails response</span><span class="sxs-lookup"><span data-stu-id="da033-243">Disable ProblemDetails response</span></span>

<span data-ttu-id="da033-244">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span><span class="sxs-lookup"><span data-stu-id="da033-244">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="da033-245">Add the following code in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="da033-245">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="da033-246">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="da033-246">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
