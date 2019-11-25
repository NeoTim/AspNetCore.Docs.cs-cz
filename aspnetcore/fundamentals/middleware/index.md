---
title: ASP.NET Core Middleware
author: rick-anderson
description: Learn about ASP.NET Core middleware and the request pipeline.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: d678f3d1f6ca10e486543a2965506236e4e61b82
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239840"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="cfe8a-103">ASP.NET Core Middleware</span><span class="sxs-lookup"><span data-stu-id="cfe8a-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="cfe8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cfe8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cfe8a-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="cfe8a-106">Each component:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-106">Each component:</span></span>

* <span data-ttu-id="cfe8a-107">Chooses whether to pass the request to the next component in the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="cfe8a-108">Can perform work before and after the next component in the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="cfe8a-109">Request delegates are used to build the request pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="cfe8a-110">The request delegates handle each HTTP request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="cfe8a-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="cfe8a-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="cfe8a-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="cfe8a-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="cfe8a-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="cfe8a-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="cfe8a-117">Create a middleware pipeline with IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="cfe8a-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="cfe8a-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="cfe8a-119">The following diagram demonstrates the concept.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="cfe8a-120">The thread of execution follows the black arrows.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-120">The thread of execution follows the black arrows.</span></span>

![Request processing pattern showing a request arriving, processing through three middlewares, and the response leaving the app.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="cfe8a-124">Each delegate can perform operations before and after the next delegate.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="cfe8a-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="cfe8a-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="cfe8a-127">This case doesn't include an actual request pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="cfe8a-128">Instead, a single anonymous function is called in response to every HTTP request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="cfe8a-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="cfe8a-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="cfe8a-131">The `next` parameter represents the next delegate in the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="cfe8a-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="cfe8a-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="cfe8a-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="cfe8a-135">Short-circuiting is often desirable because it avoids unnecessary work.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="cfe8a-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="cfe8a-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="cfe8a-138">However, see the following warning about attempting to write to a response that has already been sent.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="cfe8a-139">Don't call `next.Invoke` after the response has been sent to the client.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="cfe8a-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="cfe8a-141">For example, changes such as setting headers and a status code throw an exception.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="cfe8a-142">Writing to the response body after calling `next`:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="cfe8a-143">May cause a protocol violation.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-143">May cause a protocol violation.</span></span> <span data-ttu-id="cfe8a-144">For example, writing more than the stated `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="cfe8a-145">May corrupt the body format.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-145">May corrupt the body format.</span></span> <span data-ttu-id="cfe8a-146">For example, writing an HTML footer to a CSS file.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="cfe8a-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="cfe8a-148">Middleware order</span><span class="sxs-lookup"><span data-stu-id="cfe8a-148">Middleware order</span></span>

<span data-ttu-id="cfe8a-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="cfe8a-150">The order is **critical** for security, performance, and functionality.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-150">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="cfe8a-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="cfe8a-152">In the preceding code:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-152">In the preceding code:</span></span>

* <span data-ttu-id="cfe8a-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="cfe8a-154">Not every middleware needs to go in this exact order, but many do.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-154">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="cfe8a-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="cfe8a-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="cfe8a-157">Exception/error handling</span><span class="sxs-lookup"><span data-stu-id="cfe8a-157">Exception/error handling</span></span>
   * <span data-ttu-id="cfe8a-158">When the app runs in the Development environment:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-158">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="cfe8a-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="cfe8a-160">Database Error Page Middleware reports database runtime errors.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-160">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="cfe8a-161">When the app runs in the Production environment:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-161">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="cfe8a-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="cfe8a-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="cfe8a-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="cfe8a-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="cfe8a-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="cfe8a-167">Routing Middleware (`UseRouting`) to route requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-167">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="cfe8a-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="cfe8a-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="cfe8a-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="cfe8a-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="cfe8a-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="cfe8a-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="cfe8a-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="cfe8a-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="cfe8a-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="cfe8a-177">The Static File Middleware provides **no** authorization checks.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-177">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="cfe8a-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="cfe8a-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="cfe8a-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="cfe8a-181">Authentication doesn't short-circuit unauthenticated requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-181">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="cfe8a-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="cfe8a-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="cfe8a-184">Static files aren't compressed with this middleware order.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-184">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="cfe8a-185">The Razor Pages responses can be compressed.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-185">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="cfe8a-186">In the preceding code:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-186">In the preceding code:</span></span>

* <span data-ttu-id="cfe8a-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="cfe8a-188">Not every middleware needs to go in this exact order, but many do.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-188">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="cfe8a-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="cfe8a-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="cfe8a-191">Exception/error handling</span><span class="sxs-lookup"><span data-stu-id="cfe8a-191">Exception/error handling</span></span>
   * <span data-ttu-id="cfe8a-192">When the app runs in the Development environment:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-192">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="cfe8a-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="cfe8a-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="cfe8a-195">When the app runs in the Production environment:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-195">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="cfe8a-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="cfe8a-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="cfe8a-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="cfe8a-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="cfe8a-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="cfe8a-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="cfe8a-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="cfe8a-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="cfe8a-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="cfe8a-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="cfe8a-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="cfe8a-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="cfe8a-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="cfe8a-209">The Static File Middleware provides **no** authorization checks.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-209">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="cfe8a-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="cfe8a-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="cfe8a-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="cfe8a-213">Authentication doesn't short-circuit unauthenticated requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-213">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="cfe8a-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="cfe8a-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="cfe8a-216">Static files aren't compressed with this middleware order.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-216">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="cfe8a-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a><span data-ttu-id="cfe8a-218">Use, Run, and Map</span><span class="sxs-lookup"><span data-stu-id="cfe8a-218">Use, Run, and Map</span></span>

<span data-ttu-id="cfe8a-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="cfe8a-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span><span class="sxs-lookup"><span data-stu-id="cfe8a-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="cfe8a-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="cfe8a-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="cfe8a-223">`Map` branches the request pipeline based on matches of the given request path.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-223">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="cfe8a-224">If the request path starts with the given path, the branch is executed.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-224">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="cfe8a-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="cfe8a-226">Request</span><span class="sxs-lookup"><span data-stu-id="cfe8a-226">Request</span></span>             | <span data-ttu-id="cfe8a-227">Response</span><span class="sxs-lookup"><span data-stu-id="cfe8a-227">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="cfe8a-228">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="cfe8a-228">localhost:1234</span></span>      | <span data-ttu-id="cfe8a-229">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-229">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="cfe8a-230">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="cfe8a-230">localhost:1234/map1</span></span> | <span data-ttu-id="cfe8a-231">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="cfe8a-231">Map Test 1</span></span>                   |
| <span data-ttu-id="cfe8a-232">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="cfe8a-232">localhost:1234/map2</span></span> | <span data-ttu-id="cfe8a-233">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="cfe8a-233">Map Test 2</span></span>                   |
| <span data-ttu-id="cfe8a-234">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="cfe8a-234">localhost:1234/map3</span></span> | <span data-ttu-id="cfe8a-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-235">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="cfe8a-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="cfe8a-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="cfe8a-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="cfe8a-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="cfe8a-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="cfe8a-241">Request</span><span class="sxs-lookup"><span data-stu-id="cfe8a-241">Request</span></span>                       | <span data-ttu-id="cfe8a-242">Response</span><span class="sxs-lookup"><span data-stu-id="cfe8a-242">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="cfe8a-243">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="cfe8a-243">localhost:1234</span></span>                | <span data-ttu-id="cfe8a-244">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-244">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="cfe8a-245">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="cfe8a-245">localhost:1234/?branch=master</span></span> | <span data-ttu-id="cfe8a-246">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="cfe8a-246">Branch used = master</span></span>         |

<span data-ttu-id="cfe8a-247">`Map` supports nesting, for example:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-247">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="cfe8a-248">`Map` can also match multiple segments at once:</span><span class="sxs-lookup"><span data-stu-id="cfe8a-248">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="cfe8a-249">Built-in middleware</span><span class="sxs-lookup"><span data-stu-id="cfe8a-249">Built-in middleware</span></span>

<span data-ttu-id="cfe8a-250">ASP.NET Core ships with the following middleware components.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-250">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="cfe8a-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="cfe8a-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="cfe8a-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="cfe8a-254">Middleware</span><span class="sxs-lookup"><span data-stu-id="cfe8a-254">Middleware</span></span> | <span data-ttu-id="cfe8a-255">Popis</span><span class="sxs-lookup"><span data-stu-id="cfe8a-255">Description</span></span> | <span data-ttu-id="cfe8a-256">Order</span><span class="sxs-lookup"><span data-stu-id="cfe8a-256">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="cfe8a-257">Ověřování</span><span class="sxs-lookup"><span data-stu-id="cfe8a-257">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="cfe8a-258">Provides authentication support.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-258">Provides authentication support.</span></span> | <span data-ttu-id="cfe8a-259">Before `HttpContext.User` is needed.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-259">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="cfe8a-260">Terminal for OAuth callbacks.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-260">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="cfe8a-261">Cookie Policy</span><span class="sxs-lookup"><span data-stu-id="cfe8a-261">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="cfe8a-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="cfe8a-263">Before middleware that issues cookies.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-263">Before middleware that issues cookies.</span></span> <span data-ttu-id="cfe8a-264">Examples: Authentication, Session, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="cfe8a-264">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="cfe8a-265">CORS</span><span class="sxs-lookup"><span data-stu-id="cfe8a-265">CORS</span></span>](xref:security/cors) | <span data-ttu-id="cfe8a-266">Configures Cross-Origin Resource Sharing.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-266">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="cfe8a-267">Before components that use CORS.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-267">Before components that use CORS.</span></span> |
| [<span data-ttu-id="cfe8a-268">Diagnostika</span><span class="sxs-lookup"><span data-stu-id="cfe8a-268">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="cfe8a-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="cfe8a-270">Before components that generate errors.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-270">Before components that generate errors.</span></span> <span data-ttu-id="cfe8a-271">Terminal for exceptions or serving the default web page for new apps.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-271">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="cfe8a-272">Forwarded Headers</span><span class="sxs-lookup"><span data-stu-id="cfe8a-272">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="cfe8a-273">Forwards proxied headers onto the current request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-273">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="cfe8a-274">Before components that consume the updated fields.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-274">Before components that consume the updated fields.</span></span> <span data-ttu-id="cfe8a-275">Examples: scheme, host, client IP, method.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-275">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="cfe8a-276">Health Check</span><span class="sxs-lookup"><span data-stu-id="cfe8a-276">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="cfe8a-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="cfe8a-278">Terminal if a request matches a health check endpoint.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-278">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="cfe8a-279">HTTP Method Override</span><span class="sxs-lookup"><span data-stu-id="cfe8a-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="cfe8a-280">Allows an incoming POST request to override the method.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="cfe8a-281">Before components that consume the updated method.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="cfe8a-282">HTTPS Redirection</span><span class="sxs-lookup"><span data-stu-id="cfe8a-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="cfe8a-283">Redirect all HTTP requests to HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="cfe8a-284">Before components that consume the URL.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="cfe8a-285">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="cfe8a-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="cfe8a-286">Security enhancement middleware that adds a special response header.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="cfe8a-287">Before responses are sent and after components that modify requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="cfe8a-288">Examples: Forwarded Headers, URL Rewriting.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="cfe8a-289">MVC</span><span class="sxs-lookup"><span data-stu-id="cfe8a-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="cfe8a-290">Processes requests with MVC/Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="cfe8a-291">Terminal if a request matches a route.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="cfe8a-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="cfe8a-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="cfe8a-293">Interop with OWIN-based apps, servers, and middleware.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="cfe8a-294">Terminal if the OWIN Middleware fully processes the request.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="cfe8a-295">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="cfe8a-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="cfe8a-296">Provides support for caching responses.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-296">Provides support for caching responses.</span></span> | <span data-ttu-id="cfe8a-297">Before components that require caching.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-297">Before components that require caching.</span></span> |
| [<span data-ttu-id="cfe8a-298">Response Compression</span><span class="sxs-lookup"><span data-stu-id="cfe8a-298">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="cfe8a-299">Provides support for compressing responses.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-299">Provides support for compressing responses.</span></span> | <span data-ttu-id="cfe8a-300">Before components that require compression.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-300">Before components that require compression.</span></span> |
| [<span data-ttu-id="cfe8a-301">Request Localization</span><span class="sxs-lookup"><span data-stu-id="cfe8a-301">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="cfe8a-302">Provides localization support.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-302">Provides localization support.</span></span> | <span data-ttu-id="cfe8a-303">Before localization sensitive components.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-303">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="cfe8a-304">Endpoint Routing</span><span class="sxs-lookup"><span data-stu-id="cfe8a-304">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="cfe8a-305">Defines and constrains request routes.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-305">Defines and constrains request routes.</span></span> | <span data-ttu-id="cfe8a-306">Terminal for matching routes.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-306">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="cfe8a-307">Relace</span><span class="sxs-lookup"><span data-stu-id="cfe8a-307">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="cfe8a-308">Provides support for managing user sessions.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-308">Provides support for managing user sessions.</span></span> | <span data-ttu-id="cfe8a-309">Before components that require Session.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-309">Before components that require Session.</span></span> |
| [<span data-ttu-id="cfe8a-310">Static Files</span><span class="sxs-lookup"><span data-stu-id="cfe8a-310">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="cfe8a-311">Provides support for serving static files and directory browsing.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-311">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="cfe8a-312">Terminal if a request matches a file.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-312">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="cfe8a-313">URL Rewrite</span><span class="sxs-lookup"><span data-stu-id="cfe8a-313">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="cfe8a-314">Provides support for rewriting URLs and redirecting requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-314">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="cfe8a-315">Before components that consume the URL.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-315">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="cfe8a-316">Webové sokety</span><span class="sxs-lookup"><span data-stu-id="cfe8a-316">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="cfe8a-317">Enables the WebSockets protocol.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-317">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="cfe8a-318">Before components that are required to accept WebSocket requests.</span><span class="sxs-lookup"><span data-stu-id="cfe8a-318">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="cfe8a-319">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cfe8a-319">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
