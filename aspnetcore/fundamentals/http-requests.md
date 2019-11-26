---
title: Make HTTP requests using IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Learn about using the IHttpClientFactory interface to manage logical HttpClient instances in ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 7a5b5c84775ea2482034ef9f3e8a2376036e66cb
ms.sourcegitcommit: a104ba258ae7c0b3ee7c6fa7eaea1ddeb8b6eb73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478741"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="037f1-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="037f1-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="037f1-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="037f1-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="037f1-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span><span class="sxs-lookup"><span data-stu-id="037f1-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="037f1-106">`IHttpClientFactory` offers the following benefits:</span><span class="sxs-lookup"><span data-stu-id="037f1-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="037f1-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="037f1-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="037f1-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="037f1-109">A default client can be registered for general access.</span><span class="sxs-lookup"><span data-stu-id="037f1-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="037f1-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="037f1-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="037f1-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="037f1-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span><span class="sxs-lookup"><span data-stu-id="037f1-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="037f1-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span><span class="sxs-lookup"><span data-stu-id="037f1-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="037f1-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="037f1-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="037f1-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span><span class="sxs-lookup"><span data-stu-id="037f1-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="037f1-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span><span class="sxs-lookup"><span data-stu-id="037f1-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="037f1-118">Consumption patterns</span><span class="sxs-lookup"><span data-stu-id="037f1-118">Consumption patterns</span></span>

<span data-ttu-id="037f1-119">There are several ways `IHttpClientFactory` can be used in an app:</span><span class="sxs-lookup"><span data-stu-id="037f1-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="037f1-120">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="037f1-121">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="037f1-122">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="037f1-123">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="037f1-124">The best approach depends upon the app's requirements.</span><span class="sxs-lookup"><span data-stu-id="037f1-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="037f1-125">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-125">Basic usage</span></span>

<span data-ttu-id="037f1-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="037f1-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="037f1-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="037f1-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="037f1-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="037f1-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="037f1-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span><span class="sxs-lookup"><span data-stu-id="037f1-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="037f1-130">It has no impact on how `HttpClient` is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="037f1-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="037f1-132">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-132">Named clients</span></span>

<span data-ttu-id="037f1-133">Named clients are a good choice when:</span><span class="sxs-lookup"><span data-stu-id="037f1-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="037f1-134">The app requires many distinct uses of `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="037f1-135">Many `HttpClient`s have different configuration.</span><span class="sxs-lookup"><span data-stu-id="037f1-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="037f1-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="037f1-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="037f1-137">In the preceding code the client is configured with:</span><span class="sxs-lookup"><span data-stu-id="037f1-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="037f1-138">The base address `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="037f1-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="037f1-139">Two headers required to work with the GitHub API.</span><span class="sxs-lookup"><span data-stu-id="037f1-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="037f1-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="037f1-140">CreateClient</span></span>

<span data-ttu-id="037f1-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span><span class="sxs-lookup"><span data-stu-id="037f1-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="037f1-142">A new instance of `HttpClient` is created.</span><span class="sxs-lookup"><span data-stu-id="037f1-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="037f1-143">The configuration action is called.</span><span class="sxs-lookup"><span data-stu-id="037f1-143">The configuration action is called.</span></span>

<span data-ttu-id="037f1-144">To create a named client, pass its name into `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="037f1-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="037f1-145">In the preceding code, the request doesn't need to specify a hostname.</span><span class="sxs-lookup"><span data-stu-id="037f1-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="037f1-146">The code can pass just the path, since the base address configured for the client is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="037f1-147">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-147">Typed clients</span></span>

<span data-ttu-id="037f1-148">Typed clients:</span><span class="sxs-lookup"><span data-stu-id="037f1-148">Typed clients:</span></span>

* <span data-ttu-id="037f1-149">Provide the same capabilities as named clients without the need to use strings as keys.</span><span class="sxs-lookup"><span data-stu-id="037f1-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="037f1-150">Provides IntelliSense and compiler help when consuming clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="037f1-151">Provide a single location to configure and interact with a particular `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="037f1-152">For example, a single typed client might be used:</span><span class="sxs-lookup"><span data-stu-id="037f1-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="037f1-153">For a single backend endpoint.</span><span class="sxs-lookup"><span data-stu-id="037f1-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="037f1-154">To encapsulate all logic dealing with the endpoint.</span><span class="sxs-lookup"><span data-stu-id="037f1-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="037f1-155">Work with DI and can be injected where required in the app.</span><span class="sxs-lookup"><span data-stu-id="037f1-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="037f1-156">A typed client accepts a `HttpClient` parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="037f1-157">In the preceding code:</span><span class="sxs-lookup"><span data-stu-id="037f1-157">In the preceding code:</span></span>

* <span data-ttu-id="037f1-158">The configuration is moved into the typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="037f1-159">The `HttpClient` object is exposed as a public property.</span><span class="sxs-lookup"><span data-stu-id="037f1-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="037f1-160">API-specific methods can be created that expose `HttpClient` functionality.</span><span class="sxs-lookup"><span data-stu-id="037f1-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="037f1-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span><span class="sxs-lookup"><span data-stu-id="037f1-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="037f1-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span><span class="sxs-lookup"><span data-stu-id="037f1-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="037f1-163">The typed client is registered as transient with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="037f1-164">The typed client can be injected and consumed directly:</span><span class="sxs-lookup"><span data-stu-id="037f1-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="037f1-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="037f1-166">The `HttpClient` can be encapsulated within a typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="037f1-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span><span class="sxs-lookup"><span data-stu-id="037f1-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="037f1-168">In the preceding code, the `HttpClient` is stored in a private field.</span><span class="sxs-lookup"><span data-stu-id="037f1-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="037f1-169">Access to the `HttpClient` is by the public `GetRepos` method.</span><span class="sxs-lookup"><span data-stu-id="037f1-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="037f1-170">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-170">Generated clients</span></span>

<span data-ttu-id="037f1-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="037f1-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="037f1-172">Refit is a REST library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="037f1-173">It converts REST APIs into live interfaces.</span><span class="sxs-lookup"><span data-stu-id="037f1-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="037f1-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span><span class="sxs-lookup"><span data-stu-id="037f1-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="037f1-175">An interface and a reply are defined to represent the external API and its response:</span><span class="sxs-lookup"><span data-stu-id="037f1-175">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="037f1-176">A typed client can be added, using Refit to generate the implementation:</span><span class="sxs-lookup"><span data-stu-id="037f1-176">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="037f1-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span><span class="sxs-lookup"><span data-stu-id="037f1-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="037f1-178">Outgoing request middleware</span><span class="sxs-lookup"><span data-stu-id="037f1-178">Outgoing request middleware</span></span>

<span data-ttu-id="037f1-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="037f1-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="037f1-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="037f1-181">Simplifies defining the handlers to apply for each named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="037f1-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="037f1-183">Each of these handlers is able to perform work before and after the outgoing request.</span><span class="sxs-lookup"><span data-stu-id="037f1-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="037f1-184">This pattern:</span><span class="sxs-lookup"><span data-stu-id="037f1-184">This pattern:</span></span>

  * <span data-ttu-id="037f1-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="037f1-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="037f1-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span><span class="sxs-lookup"><span data-stu-id="037f1-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="037f1-187">ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="037f1-187">caching</span></span>
    * <span data-ttu-id="037f1-188">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="037f1-188">error handling</span></span>
    * <span data-ttu-id="037f1-189">serializace</span><span class="sxs-lookup"><span data-stu-id="037f1-189">serialization</span></span>
    * <span data-ttu-id="037f1-190">protokolování</span><span class="sxs-lookup"><span data-stu-id="037f1-190">logging</span></span>

<span data-ttu-id="037f1-191">To create a delegating handler:</span><span class="sxs-lookup"><span data-stu-id="037f1-191">To create a delegating handler:</span></span>

* <span data-ttu-id="037f1-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="037f1-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="037f1-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="037f1-194">Execute code before passing the request to the next handler in the pipeline:</span><span class="sxs-lookup"><span data-stu-id="037f1-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="037f1-195">The preceding code checks if the `X-API-KEY` header is in the request.</span><span class="sxs-lookup"><span data-stu-id="037f1-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="037f1-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span><span class="sxs-lookup"><span data-stu-id="037f1-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="037f1-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="037f1-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="037f1-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="037f1-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="037f1-200">Handlers can depend upon services of any scope.</span><span class="sxs-lookup"><span data-stu-id="037f1-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="037f1-201">Services that handlers depend upon are disposed when the handler is disposed.</span><span class="sxs-lookup"><span data-stu-id="037f1-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="037f1-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="037f1-203">Multiple handlers can be registered in the order that they should execute.</span><span class="sxs-lookup"><span data-stu-id="037f1-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="037f1-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span><span class="sxs-lookup"><span data-stu-id="037f1-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="037f1-205">Use one of the following approaches to share per-request state with message handlers:</span><span class="sxs-lookup"><span data-stu-id="037f1-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="037f1-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="037f1-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="037f1-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span><span class="sxs-lookup"><span data-stu-id="037f1-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="037f1-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span><span class="sxs-lookup"><span data-stu-id="037f1-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="037f1-209">Use Polly-based handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-209">Use Polly-based handlers</span></span>

<span data-ttu-id="037f1-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="037f1-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="037f1-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="037f1-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span><span class="sxs-lookup"><span data-stu-id="037f1-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="037f1-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="037f1-214">The Polly extensions support adding Polly-based handlers to clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="037f1-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span><span class="sxs-lookup"><span data-stu-id="037f1-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="037f1-216">Handle transient faults</span><span class="sxs-lookup"><span data-stu-id="037f1-216">Handle transient faults</span></span>

<span data-ttu-id="037f1-217">Faults typically occur when external HTTP calls are transient.</span><span class="sxs-lookup"><span data-stu-id="037f1-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="037f1-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span><span class="sxs-lookup"><span data-stu-id="037f1-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="037f1-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span><span class="sxs-lookup"><span data-stu-id="037f1-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="037f1-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="037f1-220">HTTP 5xx</span></span>
* <span data-ttu-id="037f1-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="037f1-221">HTTP 408</span></span>

<span data-ttu-id="037f1-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span><span class="sxs-lookup"><span data-stu-id="037f1-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="037f1-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span><span class="sxs-lookup"><span data-stu-id="037f1-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="037f1-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span><span class="sxs-lookup"><span data-stu-id="037f1-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="037f1-225">Dynamically select policies</span><span class="sxs-lookup"><span data-stu-id="037f1-225">Dynamically select policies</span></span>

<span data-ttu-id="037f1-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="037f1-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span><span class="sxs-lookup"><span data-stu-id="037f1-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="037f1-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span><span class="sxs-lookup"><span data-stu-id="037f1-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="037f1-229">For any other HTTP method, a 30-second timeout is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="037f1-230">Add multiple Polly handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="037f1-231">It's common to nest Polly policies:</span><span class="sxs-lookup"><span data-stu-id="037f1-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="037f1-232">In the preceding example:</span><span class="sxs-lookup"><span data-stu-id="037f1-232">In the preceding example:</span></span>

* <span data-ttu-id="037f1-233">Two handlers are added.</span><span class="sxs-lookup"><span data-stu-id="037f1-233">Two handlers are added.</span></span>
* <span data-ttu-id="037f1-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="037f1-235">Failed requests are retried up to three times.</span><span class="sxs-lookup"><span data-stu-id="037f1-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="037f1-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="037f1-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span><span class="sxs-lookup"><span data-stu-id="037f1-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="037f1-238">Circuit breaker policies are stateful.</span><span class="sxs-lookup"><span data-stu-id="037f1-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="037f1-239">All calls through this client share the same circuit state.</span><span class="sxs-lookup"><span data-stu-id="037f1-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="037f1-240">Add policies from the Polly registry</span><span class="sxs-lookup"><span data-stu-id="037f1-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="037f1-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="037f1-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="037f1-242">In the following code:</span><span class="sxs-lookup"><span data-stu-id="037f1-242">In the following code:</span></span>

* <span data-ttu-id="037f1-243">The "regular" and "long" polices are added.</span><span class="sxs-lookup"><span data-stu-id="037f1-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="037f1-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span><span class="sxs-lookup"><span data-stu-id="037f1-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="037f1-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="037f1-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="037f1-246">HttpClient and lifetime management</span><span class="sxs-lookup"><span data-stu-id="037f1-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="037f1-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="037f1-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="037f1-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span><span class="sxs-lookup"><span data-stu-id="037f1-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="037f1-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span><span class="sxs-lookup"><span data-stu-id="037f1-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="037f1-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span><span class="sxs-lookup"><span data-stu-id="037f1-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="037f1-253">Creating more handlers than necessary can result in connection delays.</span><span class="sxs-lookup"><span data-stu-id="037f1-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="037f1-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span><span class="sxs-lookup"><span data-stu-id="037f1-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="037f1-255">The default handler lifetime is two minutes.</span><span class="sxs-lookup"><span data-stu-id="037f1-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="037f1-256">The default value can be overridden on a per named client basis:</span><span class="sxs-lookup"><span data-stu-id="037f1-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="037f1-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span><span class="sxs-lookup"><span data-stu-id="037f1-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="037f1-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="037f1-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="037f1-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="037f1-262">Alternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="037f1-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="037f1-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="037f1-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="037f1-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="037f1-265">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-265">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="037f1-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="037f1-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="037f1-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span><span class="sxs-lookup"><span data-stu-id="037f1-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="037f1-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span><span class="sxs-lookup"><span data-stu-id="037f1-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="037f1-269">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span><span class="sxs-lookup"><span data-stu-id="037f1-269">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="037f1-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span><span class="sxs-lookup"><span data-stu-id="037f1-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="037f1-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="037f1-272">This sharing prevents socket exhaustion.</span><span class="sxs-lookup"><span data-stu-id="037f1-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="037f1-273">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span><span class="sxs-lookup"><span data-stu-id="037f1-273">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="037f1-274">Cookies</span><span class="sxs-lookup"><span data-stu-id="037f1-274">Cookies</span></span>

<span data-ttu-id="037f1-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span><span class="sxs-lookup"><span data-stu-id="037f1-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="037f1-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span><span class="sxs-lookup"><span data-stu-id="037f1-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="037f1-277">For apps that require cookies, consider either:</span><span class="sxs-lookup"><span data-stu-id="037f1-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="037f1-278">Disabling automatic cookie handling</span><span class="sxs-lookup"><span data-stu-id="037f1-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="037f1-279">Avoiding `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="037f1-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="037f1-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span><span class="sxs-lookup"><span data-stu-id="037f1-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="037f1-281">protokolování</span><span class="sxs-lookup"><span data-stu-id="037f1-281">Logging</span></span>

<span data-ttu-id="037f1-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="037f1-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span><span class="sxs-lookup"><span data-stu-id="037f1-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="037f1-284">Additional logging, such as the logging of request headers, is only included at trace level.</span><span class="sxs-lookup"><span data-stu-id="037f1-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="037f1-285">The log category used for each client includes the name of the client.</span><span class="sxs-lookup"><span data-stu-id="037f1-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="037f1-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="037f1-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="037f1-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="037f1-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span><span class="sxs-lookup"><span data-stu-id="037f1-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="037f1-289">On the response, messages are logged after any other pipeline handlers have received the response.</span><span class="sxs-lookup"><span data-stu-id="037f1-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="037f1-290">Logging also occurs inside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="037f1-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="037f1-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="037f1-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span><span class="sxs-lookup"><span data-stu-id="037f1-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="037f1-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="037f1-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span><span class="sxs-lookup"><span data-stu-id="037f1-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="037f1-295">This may include changes to request headers or to the response status code.</span><span class="sxs-lookup"><span data-stu-id="037f1-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="037f1-296">Including the name of the client in the log category enables log filtering for specific named clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="037f1-297">Configure the HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="037f1-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="037f1-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span><span class="sxs-lookup"><span data-stu-id="037f1-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="037f1-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="037f1-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span><span class="sxs-lookup"><span data-stu-id="037f1-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="037f1-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span><span class="sxs-lookup"><span data-stu-id="037f1-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="037f1-302">Use IHttpClientFactory in a console app</span><span class="sxs-lookup"><span data-stu-id="037f1-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="037f1-303">In a console app, add the following package references to the project:</span><span class="sxs-lookup"><span data-stu-id="037f1-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="037f1-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="037f1-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="037f1-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="037f1-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="037f1-306">In the following example:</span><span class="sxs-lookup"><span data-stu-id="037f1-306">In the following example:</span></span>

* <span data-ttu-id="037f1-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span><span class="sxs-lookup"><span data-stu-id="037f1-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="037f1-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="037f1-309">`HttpClient` is used to retrieve a webpage.</span><span class="sxs-lookup"><span data-stu-id="037f1-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="037f1-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span><span class="sxs-lookup"><span data-stu-id="037f1-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="037f1-311">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="037f1-311">Additional resources</span></span>

* [<span data-ttu-id="037f1-312">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="037f1-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="037f1-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span><span class="sxs-lookup"><span data-stu-id="037f1-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="037f1-314">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="037f1-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="037f1-315">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="037f1-315">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="037f1-316">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span><span class="sxs-lookup"><span data-stu-id="037f1-316">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="037f1-317">It offers the following benefits:</span><span class="sxs-lookup"><span data-stu-id="037f1-317">It offers the following benefits:</span></span>

* <span data-ttu-id="037f1-318">Provides a central location for naming and configuring logical `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-318">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="037f1-319">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="037f1-319">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="037f1-320">A default client can be registered for other purposes.</span><span class="sxs-lookup"><span data-stu-id="037f1-320">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="037f1-321">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span><span class="sxs-lookup"><span data-stu-id="037f1-321">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="037f1-322">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span><span class="sxs-lookup"><span data-stu-id="037f1-322">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="037f1-323">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span><span class="sxs-lookup"><span data-stu-id="037f1-323">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="037f1-324">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="037f1-324">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="037f1-325">Consumption patterns</span><span class="sxs-lookup"><span data-stu-id="037f1-325">Consumption patterns</span></span>

<span data-ttu-id="037f1-326">There are several ways `IHttpClientFactory` can be used in an app:</span><span class="sxs-lookup"><span data-stu-id="037f1-326">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="037f1-327">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-327">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="037f1-328">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-328">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="037f1-329">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-329">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="037f1-330">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-330">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="037f1-331">None of them are strictly superior to another.</span><span class="sxs-lookup"><span data-stu-id="037f1-331">None of them are strictly superior to another.</span></span> <span data-ttu-id="037f1-332">The best approach depends upon the app's constraints.</span><span class="sxs-lookup"><span data-stu-id="037f1-332">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="037f1-333">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-333">Basic usage</span></span>

<span data-ttu-id="037f1-334">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span><span class="sxs-lookup"><span data-stu-id="037f1-334">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="037f1-335">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="037f1-335">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="037f1-336">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="037f1-336">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="037f1-337">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span><span class="sxs-lookup"><span data-stu-id="037f1-337">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="037f1-338">It has no impact on the way `HttpClient` is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-338">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="037f1-339">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-339">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="037f1-340">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-340">Named clients</span></span>

<span data-ttu-id="037f1-341">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span><span class="sxs-lookup"><span data-stu-id="037f1-341">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="037f1-342">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="037f1-342">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="037f1-343">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span><span class="sxs-lookup"><span data-stu-id="037f1-343">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="037f1-344">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span><span class="sxs-lookup"><span data-stu-id="037f1-344">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="037f1-345">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span><span class="sxs-lookup"><span data-stu-id="037f1-345">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="037f1-346">To consume a named client, a string parameter can be passed to `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-346">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="037f1-347">Specify the name of the client to be created:</span><span class="sxs-lookup"><span data-stu-id="037f1-347">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="037f1-348">In the preceding code, the request doesn't need to specify a hostname.</span><span class="sxs-lookup"><span data-stu-id="037f1-348">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="037f1-349">It can pass just the path, since the base address configured for the client is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-349">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="037f1-350">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-350">Typed clients</span></span>

<span data-ttu-id="037f1-351">Typed clients:</span><span class="sxs-lookup"><span data-stu-id="037f1-351">Typed clients:</span></span>

* <span data-ttu-id="037f1-352">Provide the same capabilities as named clients without the need to use strings as keys.</span><span class="sxs-lookup"><span data-stu-id="037f1-352">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="037f1-353">Provides IntelliSense and compiler help when consuming clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-353">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="037f1-354">Provide a single location to configure and interact with a particular `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-354">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="037f1-355">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span><span class="sxs-lookup"><span data-stu-id="037f1-355">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="037f1-356">Work with DI and can be injected where required in your app.</span><span class="sxs-lookup"><span data-stu-id="037f1-356">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="037f1-357">A typed client accepts a `HttpClient` parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-357">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="037f1-358">In the preceding code, the configuration is moved into the typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-358">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="037f1-359">The `HttpClient` object is exposed as a public property.</span><span class="sxs-lookup"><span data-stu-id="037f1-359">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="037f1-360">It's possible to define API-specific methods that expose `HttpClient` functionality.</span><span class="sxs-lookup"><span data-stu-id="037f1-360">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="037f1-361">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span><span class="sxs-lookup"><span data-stu-id="037f1-361">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="037f1-362">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span><span class="sxs-lookup"><span data-stu-id="037f1-362">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="037f1-363">The typed client is registered as transient with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-363">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="037f1-364">The typed client can be injected and consumed directly:</span><span class="sxs-lookup"><span data-stu-id="037f1-364">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="037f1-365">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-365">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="037f1-366">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-366">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="037f1-367">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span><span class="sxs-lookup"><span data-stu-id="037f1-367">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="037f1-368">In the preceding code, the `HttpClient` is stored as a private field.</span><span class="sxs-lookup"><span data-stu-id="037f1-368">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="037f1-369">All access to make external calls goes through the `GetRepos` method.</span><span class="sxs-lookup"><span data-stu-id="037f1-369">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="037f1-370">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-370">Generated clients</span></span>

<span data-ttu-id="037f1-371">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="037f1-371">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="037f1-372">Refit is a REST library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-372">Refit is a REST library for .NET.</span></span> <span data-ttu-id="037f1-373">It converts REST APIs into live interfaces.</span><span class="sxs-lookup"><span data-stu-id="037f1-373">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="037f1-374">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span><span class="sxs-lookup"><span data-stu-id="037f1-374">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="037f1-375">An interface and a reply are defined to represent the external API and its response:</span><span class="sxs-lookup"><span data-stu-id="037f1-375">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="037f1-376">A typed client can be added, using Refit to generate the implementation:</span><span class="sxs-lookup"><span data-stu-id="037f1-376">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="037f1-377">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span><span class="sxs-lookup"><span data-stu-id="037f1-377">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="037f1-378">Outgoing request middleware</span><span class="sxs-lookup"><span data-stu-id="037f1-378">Outgoing request middleware</span></span>

<span data-ttu-id="037f1-379">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-379">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="037f1-380">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-380">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="037f1-381">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-381">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="037f1-382">Each of these handlers is able to perform work before and after the outgoing request.</span><span class="sxs-lookup"><span data-stu-id="037f1-382">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="037f1-383">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="037f1-383">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="037f1-384">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span><span class="sxs-lookup"><span data-stu-id="037f1-384">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="037f1-385">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="037f1-385">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="037f1-386">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span><span class="sxs-lookup"><span data-stu-id="037f1-386">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="037f1-387">The preceding code defines a basic handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-387">The preceding code defines a basic handler.</span></span> <span data-ttu-id="037f1-388">It checks to see if an `X-API-KEY` header has been included on the request.</span><span class="sxs-lookup"><span data-stu-id="037f1-388">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="037f1-389">If the header is missing, it can avoid the HTTP call and return a suitable response.</span><span class="sxs-lookup"><span data-stu-id="037f1-389">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="037f1-390">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-390">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="037f1-391">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="037f1-391">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="037f1-392">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-392">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="037f1-393">The `IHttpClientFactory` creates a separate DI scope for each handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-393">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="037f1-394">Handlers are free to depend upon services of any scope.</span><span class="sxs-lookup"><span data-stu-id="037f1-394">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="037f1-395">Services that handlers depend upon are disposed when the handler is disposed.</span><span class="sxs-lookup"><span data-stu-id="037f1-395">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="037f1-396">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-396">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="037f1-397">Multiple handlers can be registered in the order that they should execute.</span><span class="sxs-lookup"><span data-stu-id="037f1-397">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="037f1-398">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span><span class="sxs-lookup"><span data-stu-id="037f1-398">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="037f1-399">Use one of the following approaches to share per-request state with message handlers:</span><span class="sxs-lookup"><span data-stu-id="037f1-399">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="037f1-400">Pass data into the handler using `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="037f1-400">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="037f1-401">Use `IHttpContextAccessor` to access the current request.</span><span class="sxs-lookup"><span data-stu-id="037f1-401">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="037f1-402">Create a custom `AsyncLocal` storage object to pass the data.</span><span class="sxs-lookup"><span data-stu-id="037f1-402">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="037f1-403">Use Polly-based handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-403">Use Polly-based handlers</span></span>

<span data-ttu-id="037f1-404">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="037f1-404">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="037f1-405">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-405">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="037f1-406">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span><span class="sxs-lookup"><span data-stu-id="037f1-406">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="037f1-407">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-407">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="037f1-408">The Polly extensions:</span><span class="sxs-lookup"><span data-stu-id="037f1-408">The Polly extensions:</span></span>

* <span data-ttu-id="037f1-409">Support adding Polly-based handlers to clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-409">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="037f1-410">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span><span class="sxs-lookup"><span data-stu-id="037f1-410">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="037f1-411">The package isn't included in the ASP.NET Core shared framework.</span><span class="sxs-lookup"><span data-stu-id="037f1-411">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="037f1-412">Handle transient faults</span><span class="sxs-lookup"><span data-stu-id="037f1-412">Handle transient faults</span></span>

<span data-ttu-id="037f1-413">Most common faults occur when external HTTP calls are transient.</span><span class="sxs-lookup"><span data-stu-id="037f1-413">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="037f1-414">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span><span class="sxs-lookup"><span data-stu-id="037f1-414">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="037f1-415">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span><span class="sxs-lookup"><span data-stu-id="037f1-415">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="037f1-416">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="037f1-416">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="037f1-417">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span><span class="sxs-lookup"><span data-stu-id="037f1-417">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="037f1-418">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span><span class="sxs-lookup"><span data-stu-id="037f1-418">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="037f1-419">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span><span class="sxs-lookup"><span data-stu-id="037f1-419">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="037f1-420">Dynamically select policies</span><span class="sxs-lookup"><span data-stu-id="037f1-420">Dynamically select policies</span></span>

<span data-ttu-id="037f1-421">Additional extension methods exist which can be used to add Polly-based handlers.</span><span class="sxs-lookup"><span data-stu-id="037f1-421">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="037f1-422">One such extension is `AddPolicyHandler`, which has multiple overloads.</span><span class="sxs-lookup"><span data-stu-id="037f1-422">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="037f1-423">One overload allows the request to be inspected when defining which policy to apply:</span><span class="sxs-lookup"><span data-stu-id="037f1-423">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="037f1-424">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span><span class="sxs-lookup"><span data-stu-id="037f1-424">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="037f1-425">For any other HTTP method, a 30-second timeout is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-425">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="037f1-426">Add multiple Polly handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-426">Add multiple Polly handlers</span></span>

<span data-ttu-id="037f1-427">It's common to nest Polly policies to provide enhanced functionality:</span><span class="sxs-lookup"><span data-stu-id="037f1-427">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="037f1-428">In the preceding example, two handlers are added.</span><span class="sxs-lookup"><span data-stu-id="037f1-428">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="037f1-429">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-429">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="037f1-430">Failed requests are retried up to three times.</span><span class="sxs-lookup"><span data-stu-id="037f1-430">Failed requests are retried up to three times.</span></span> <span data-ttu-id="037f1-431">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-431">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="037f1-432">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span><span class="sxs-lookup"><span data-stu-id="037f1-432">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="037f1-433">Circuit breaker policies are stateful.</span><span class="sxs-lookup"><span data-stu-id="037f1-433">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="037f1-434">All calls through this client share the same circuit state.</span><span class="sxs-lookup"><span data-stu-id="037f1-434">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="037f1-435">Add policies from the Polly registry</span><span class="sxs-lookup"><span data-stu-id="037f1-435">Add policies from the Polly registry</span></span>

<span data-ttu-id="037f1-436">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="037f1-436">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="037f1-437">An extension method is provided which allows a handler to be added using a policy from the registry:</span><span class="sxs-lookup"><span data-stu-id="037f1-437">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="037f1-438">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="037f1-438">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="037f1-439">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span><span class="sxs-lookup"><span data-stu-id="037f1-439">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="037f1-440">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="037f1-440">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="037f1-441">HttpClient and lifetime management</span><span class="sxs-lookup"><span data-stu-id="037f1-441">HttpClient and lifetime management</span></span>

<span data-ttu-id="037f1-442">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-442">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-443">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-443">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="037f1-444">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-444">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="037f1-445">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span><span class="sxs-lookup"><span data-stu-id="037f1-445">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="037f1-446">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span><span class="sxs-lookup"><span data-stu-id="037f1-446">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="037f1-447">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span><span class="sxs-lookup"><span data-stu-id="037f1-447">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="037f1-448">Creating more handlers than necessary can result in connection delays.</span><span class="sxs-lookup"><span data-stu-id="037f1-448">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="037f1-449">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span><span class="sxs-lookup"><span data-stu-id="037f1-449">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="037f1-450">The default handler lifetime is two minutes.</span><span class="sxs-lookup"><span data-stu-id="037f1-450">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="037f1-451">The default value can be overridden on a per named client basis.</span><span class="sxs-lookup"><span data-stu-id="037f1-451">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="037f1-452">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span><span class="sxs-lookup"><span data-stu-id="037f1-452">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="037f1-453">Disposal of the client isn't required.</span><span class="sxs-lookup"><span data-stu-id="037f1-453">Disposal of the client isn't required.</span></span> <span data-ttu-id="037f1-454">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-454">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="037f1-455">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-455">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="037f1-456">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span><span class="sxs-lookup"><span data-stu-id="037f1-456">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="037f1-457">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-457">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-458">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-458">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="037f1-459">Alternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="037f1-459">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="037f1-460">Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="037f1-460">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="037f1-461">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-461">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="037f1-462">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-462">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="037f1-463">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="037f1-463">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="037f1-464">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span><span class="sxs-lookup"><span data-stu-id="037f1-464">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="037f1-465">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span><span class="sxs-lookup"><span data-stu-id="037f1-465">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="037f1-466">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span><span class="sxs-lookup"><span data-stu-id="037f1-466">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="037f1-467">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span><span class="sxs-lookup"><span data-stu-id="037f1-467">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="037f1-468">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-468">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="037f1-469">This sharing prevents socket exhaustion.</span><span class="sxs-lookup"><span data-stu-id="037f1-469">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="037f1-470">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span><span class="sxs-lookup"><span data-stu-id="037f1-470">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="037f1-471">Cookies</span><span class="sxs-lookup"><span data-stu-id="037f1-471">Cookies</span></span>

<span data-ttu-id="037f1-472">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span><span class="sxs-lookup"><span data-stu-id="037f1-472">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="037f1-473">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span><span class="sxs-lookup"><span data-stu-id="037f1-473">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="037f1-474">For apps that require cookies, consider either:</span><span class="sxs-lookup"><span data-stu-id="037f1-474">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="037f1-475">Disabling automatic cookie handling</span><span class="sxs-lookup"><span data-stu-id="037f1-475">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="037f1-476">Avoiding `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="037f1-476">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="037f1-477">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span><span class="sxs-lookup"><span data-stu-id="037f1-477">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="037f1-478">protokolování</span><span class="sxs-lookup"><span data-stu-id="037f1-478">Logging</span></span>

<span data-ttu-id="037f1-479">Clients created via `IHttpClientFactory` record log messages for all requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-479">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="037f1-480">Enable the appropriate information level in your logging configuration to see the default log messages.</span><span class="sxs-lookup"><span data-stu-id="037f1-480">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="037f1-481">Additional logging, such as the logging of request headers, is only included at trace level.</span><span class="sxs-lookup"><span data-stu-id="037f1-481">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="037f1-482">The log category used for each client includes the name of the client.</span><span class="sxs-lookup"><span data-stu-id="037f1-482">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="037f1-483">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="037f1-483">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="037f1-484">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-484">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="037f1-485">On the request, messages are logged before any other handlers in the pipeline have processed it.</span><span class="sxs-lookup"><span data-stu-id="037f1-485">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="037f1-486">On the response, messages are logged after any other pipeline handlers have received the response.</span><span class="sxs-lookup"><span data-stu-id="037f1-486">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="037f1-487">Logging also occurs inside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-487">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="037f1-488">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="037f1-488">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="037f1-489">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span><span class="sxs-lookup"><span data-stu-id="037f1-489">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="037f1-490">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-490">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="037f1-491">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span><span class="sxs-lookup"><span data-stu-id="037f1-491">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="037f1-492">This may include changes to request headers, for example, or to the response status code.</span><span class="sxs-lookup"><span data-stu-id="037f1-492">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="037f1-493">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span><span class="sxs-lookup"><span data-stu-id="037f1-493">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="037f1-494">Configure the HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="037f1-494">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="037f1-495">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span><span class="sxs-lookup"><span data-stu-id="037f1-495">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="037f1-496">An `IHttpClientBuilder` is returned when adding named or typed clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-496">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="037f1-497">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span><span class="sxs-lookup"><span data-stu-id="037f1-497">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="037f1-498">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span><span class="sxs-lookup"><span data-stu-id="037f1-498">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="037f1-499">Use IHttpClientFactory in a console app</span><span class="sxs-lookup"><span data-stu-id="037f1-499">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="037f1-500">In a console app, add the following package references to the project:</span><span class="sxs-lookup"><span data-stu-id="037f1-500">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="037f1-501">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="037f1-501">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="037f1-502">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="037f1-502">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="037f1-503">In the following example:</span><span class="sxs-lookup"><span data-stu-id="037f1-503">In the following example:</span></span>

* <span data-ttu-id="037f1-504"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span><span class="sxs-lookup"><span data-stu-id="037f1-504"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="037f1-505">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-505">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="037f1-506">`HttpClient` is used to retrieve a webpage.</span><span class="sxs-lookup"><span data-stu-id="037f1-506">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="037f1-507">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span><span class="sxs-lookup"><span data-stu-id="037f1-507">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="037f1-508">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="037f1-508">Additional resources</span></span>

* [<span data-ttu-id="037f1-509">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="037f1-509">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="037f1-510">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span><span class="sxs-lookup"><span data-stu-id="037f1-510">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="037f1-511">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="037f1-511">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="037f1-512">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="037f1-512">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="037f1-513">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span><span class="sxs-lookup"><span data-stu-id="037f1-513">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="037f1-514">It offers the following benefits:</span><span class="sxs-lookup"><span data-stu-id="037f1-514">It offers the following benefits:</span></span>

* <span data-ttu-id="037f1-515">Provides a central location for naming and configuring logical `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-515">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="037f1-516">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="037f1-516">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="037f1-517">A default client can be registered for other purposes.</span><span class="sxs-lookup"><span data-stu-id="037f1-517">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="037f1-518">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span><span class="sxs-lookup"><span data-stu-id="037f1-518">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="037f1-519">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span><span class="sxs-lookup"><span data-stu-id="037f1-519">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="037f1-520">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span><span class="sxs-lookup"><span data-stu-id="037f1-520">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="037f1-521">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="037f1-521">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="037f1-522">Požadavky</span><span class="sxs-lookup"><span data-stu-id="037f1-522">Prerequisites</span></span>

<span data-ttu-id="037f1-523">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span><span class="sxs-lookup"><span data-stu-id="037f1-523">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="037f1-524">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span><span class="sxs-lookup"><span data-stu-id="037f1-524">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="037f1-525">Consumption patterns</span><span class="sxs-lookup"><span data-stu-id="037f1-525">Consumption patterns</span></span>

<span data-ttu-id="037f1-526">There are several ways `IHttpClientFactory` can be used in an app:</span><span class="sxs-lookup"><span data-stu-id="037f1-526">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="037f1-527">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-527">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="037f1-528">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-528">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="037f1-529">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-529">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="037f1-530">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-530">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="037f1-531">None of them are strictly superior to another.</span><span class="sxs-lookup"><span data-stu-id="037f1-531">None of them are strictly superior to another.</span></span> <span data-ttu-id="037f1-532">The best approach depends upon the app's constraints.</span><span class="sxs-lookup"><span data-stu-id="037f1-532">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="037f1-533">Basic usage</span><span class="sxs-lookup"><span data-stu-id="037f1-533">Basic usage</span></span>

<span data-ttu-id="037f1-534">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span><span class="sxs-lookup"><span data-stu-id="037f1-534">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="037f1-535">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="037f1-535">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="037f1-536">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="037f1-536">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="037f1-537">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span><span class="sxs-lookup"><span data-stu-id="037f1-537">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="037f1-538">It has no impact on the way `HttpClient` is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-538">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="037f1-539">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-539">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="037f1-540">Named clients</span><span class="sxs-lookup"><span data-stu-id="037f1-540">Named clients</span></span>

<span data-ttu-id="037f1-541">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span><span class="sxs-lookup"><span data-stu-id="037f1-541">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="037f1-542">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="037f1-542">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="037f1-543">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span><span class="sxs-lookup"><span data-stu-id="037f1-543">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="037f1-544">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span><span class="sxs-lookup"><span data-stu-id="037f1-544">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="037f1-545">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span><span class="sxs-lookup"><span data-stu-id="037f1-545">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="037f1-546">To consume a named client, a string parameter can be passed to `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-546">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="037f1-547">Specify the name of the client to be created:</span><span class="sxs-lookup"><span data-stu-id="037f1-547">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="037f1-548">In the preceding code, the request doesn't need to specify a hostname.</span><span class="sxs-lookup"><span data-stu-id="037f1-548">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="037f1-549">It can pass just the path, since the base address configured for the client is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-549">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="037f1-550">Typed clients</span><span class="sxs-lookup"><span data-stu-id="037f1-550">Typed clients</span></span>

<span data-ttu-id="037f1-551">Typed clients:</span><span class="sxs-lookup"><span data-stu-id="037f1-551">Typed clients:</span></span>

* <span data-ttu-id="037f1-552">Provide the same capabilities as named clients without the need to use strings as keys.</span><span class="sxs-lookup"><span data-stu-id="037f1-552">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="037f1-553">Provides IntelliSense and compiler help when consuming clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-553">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="037f1-554">Provide a single location to configure and interact with a particular `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-554">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="037f1-555">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span><span class="sxs-lookup"><span data-stu-id="037f1-555">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="037f1-556">Work with DI and can be injected where required in your app.</span><span class="sxs-lookup"><span data-stu-id="037f1-556">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="037f1-557">A typed client accepts a `HttpClient` parameter in its constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-557">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="037f1-558">In the preceding code, the configuration is moved into the typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-558">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="037f1-559">The `HttpClient` object is exposed as a public property.</span><span class="sxs-lookup"><span data-stu-id="037f1-559">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="037f1-560">It's possible to define API-specific methods that expose `HttpClient` functionality.</span><span class="sxs-lookup"><span data-stu-id="037f1-560">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="037f1-561">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span><span class="sxs-lookup"><span data-stu-id="037f1-561">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="037f1-562">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span><span class="sxs-lookup"><span data-stu-id="037f1-562">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="037f1-563">The typed client is registered as transient with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-563">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="037f1-564">The typed client can be injected and consumed directly:</span><span class="sxs-lookup"><span data-stu-id="037f1-564">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="037f1-565">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span><span class="sxs-lookup"><span data-stu-id="037f1-565">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="037f1-566">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span><span class="sxs-lookup"><span data-stu-id="037f1-566">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="037f1-567">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span><span class="sxs-lookup"><span data-stu-id="037f1-567">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="037f1-568">In the preceding code, the `HttpClient` is stored as a private field.</span><span class="sxs-lookup"><span data-stu-id="037f1-568">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="037f1-569">All access to make external calls goes through the `GetRepos` method.</span><span class="sxs-lookup"><span data-stu-id="037f1-569">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="037f1-570">Generated clients</span><span class="sxs-lookup"><span data-stu-id="037f1-570">Generated clients</span></span>

<span data-ttu-id="037f1-571">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="037f1-571">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="037f1-572">Refit is a REST library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-572">Refit is a REST library for .NET.</span></span> <span data-ttu-id="037f1-573">It converts REST APIs into live interfaces.</span><span class="sxs-lookup"><span data-stu-id="037f1-573">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="037f1-574">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span><span class="sxs-lookup"><span data-stu-id="037f1-574">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="037f1-575">An interface and a reply are defined to represent the external API and its response:</span><span class="sxs-lookup"><span data-stu-id="037f1-575">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="037f1-576">A typed client can be added, using Refit to generate the implementation:</span><span class="sxs-lookup"><span data-stu-id="037f1-576">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="037f1-577">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span><span class="sxs-lookup"><span data-stu-id="037f1-577">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="037f1-578">Outgoing request middleware</span><span class="sxs-lookup"><span data-stu-id="037f1-578">Outgoing request middleware</span></span>

<span data-ttu-id="037f1-579">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-579">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="037f1-580">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-580">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="037f1-581">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-581">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="037f1-582">Each of these handlers is able to perform work before and after the outgoing request.</span><span class="sxs-lookup"><span data-stu-id="037f1-582">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="037f1-583">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="037f1-583">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="037f1-584">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span><span class="sxs-lookup"><span data-stu-id="037f1-584">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="037f1-585">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="037f1-585">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="037f1-586">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span><span class="sxs-lookup"><span data-stu-id="037f1-586">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="037f1-587">The preceding code defines a basic handler.</span><span class="sxs-lookup"><span data-stu-id="037f1-587">The preceding code defines a basic handler.</span></span> <span data-ttu-id="037f1-588">It checks to see if an `X-API-KEY` header has been included on the request.</span><span class="sxs-lookup"><span data-stu-id="037f1-588">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="037f1-589">If the header is missing, it can avoid the HTTP call and return a suitable response.</span><span class="sxs-lookup"><span data-stu-id="037f1-589">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="037f1-590">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-590">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="037f1-591">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="037f1-591">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="037f1-592">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span><span class="sxs-lookup"><span data-stu-id="037f1-592">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="037f1-593">The handler **must** be registered in DI as a transient service, never scoped.</span><span class="sxs-lookup"><span data-stu-id="037f1-593">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="037f1-594">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span><span class="sxs-lookup"><span data-stu-id="037f1-594">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="037f1-595">The handler's services could be disposed before the handler goes out of scope.</span><span class="sxs-lookup"><span data-stu-id="037f1-595">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="037f1-596">The disposed handler services causes the handler to fail.</span><span class="sxs-lookup"><span data-stu-id="037f1-596">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="037f1-597">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span><span class="sxs-lookup"><span data-stu-id="037f1-597">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="037f1-598">Multiple handlers can be registered in the order that they should execute.</span><span class="sxs-lookup"><span data-stu-id="037f1-598">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="037f1-599">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span><span class="sxs-lookup"><span data-stu-id="037f1-599">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="037f1-600">Use one of the following approaches to share per-request state with message handlers:</span><span class="sxs-lookup"><span data-stu-id="037f1-600">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="037f1-601">Pass data into the handler using `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="037f1-601">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="037f1-602">Use `IHttpContextAccessor` to access the current request.</span><span class="sxs-lookup"><span data-stu-id="037f1-602">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="037f1-603">Create a custom `AsyncLocal` storage object to pass the data.</span><span class="sxs-lookup"><span data-stu-id="037f1-603">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="037f1-604">Use Polly-based handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-604">Use Polly-based handlers</span></span>

<span data-ttu-id="037f1-605">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="037f1-605">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="037f1-606">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span><span class="sxs-lookup"><span data-stu-id="037f1-606">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="037f1-607">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span><span class="sxs-lookup"><span data-stu-id="037f1-607">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="037f1-608">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-608">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="037f1-609">The Polly extensions:</span><span class="sxs-lookup"><span data-stu-id="037f1-609">The Polly extensions:</span></span>

* <span data-ttu-id="037f1-610">Support adding Polly-based handlers to clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-610">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="037f1-611">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span><span class="sxs-lookup"><span data-stu-id="037f1-611">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="037f1-612">The package isn't included in the ASP.NET Core shared framework.</span><span class="sxs-lookup"><span data-stu-id="037f1-612">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="037f1-613">Handle transient faults</span><span class="sxs-lookup"><span data-stu-id="037f1-613">Handle transient faults</span></span>

<span data-ttu-id="037f1-614">Most common faults occur when external HTTP calls are transient.</span><span class="sxs-lookup"><span data-stu-id="037f1-614">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="037f1-615">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span><span class="sxs-lookup"><span data-stu-id="037f1-615">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="037f1-616">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span><span class="sxs-lookup"><span data-stu-id="037f1-616">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="037f1-617">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="037f1-617">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="037f1-618">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span><span class="sxs-lookup"><span data-stu-id="037f1-618">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="037f1-619">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span><span class="sxs-lookup"><span data-stu-id="037f1-619">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="037f1-620">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span><span class="sxs-lookup"><span data-stu-id="037f1-620">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="037f1-621">Dynamically select policies</span><span class="sxs-lookup"><span data-stu-id="037f1-621">Dynamically select policies</span></span>

<span data-ttu-id="037f1-622">Additional extension methods exist which can be used to add Polly-based handlers.</span><span class="sxs-lookup"><span data-stu-id="037f1-622">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="037f1-623">One such extension is `AddPolicyHandler`, which has multiple overloads.</span><span class="sxs-lookup"><span data-stu-id="037f1-623">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="037f1-624">One overload allows the request to be inspected when defining which policy to apply:</span><span class="sxs-lookup"><span data-stu-id="037f1-624">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="037f1-625">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span><span class="sxs-lookup"><span data-stu-id="037f1-625">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="037f1-626">For any other HTTP method, a 30-second timeout is used.</span><span class="sxs-lookup"><span data-stu-id="037f1-626">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="037f1-627">Add multiple Polly handlers</span><span class="sxs-lookup"><span data-stu-id="037f1-627">Add multiple Polly handlers</span></span>

<span data-ttu-id="037f1-628">It's common to nest Polly policies to provide enhanced functionality:</span><span class="sxs-lookup"><span data-stu-id="037f1-628">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="037f1-629">In the preceding example, two handlers are added.</span><span class="sxs-lookup"><span data-stu-id="037f1-629">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="037f1-630">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-630">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="037f1-631">Failed requests are retried up to three times.</span><span class="sxs-lookup"><span data-stu-id="037f1-631">Failed requests are retried up to three times.</span></span> <span data-ttu-id="037f1-632">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span><span class="sxs-lookup"><span data-stu-id="037f1-632">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="037f1-633">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span><span class="sxs-lookup"><span data-stu-id="037f1-633">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="037f1-634">Circuit breaker policies are stateful.</span><span class="sxs-lookup"><span data-stu-id="037f1-634">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="037f1-635">All calls through this client share the same circuit state.</span><span class="sxs-lookup"><span data-stu-id="037f1-635">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="037f1-636">Add policies from the Polly registry</span><span class="sxs-lookup"><span data-stu-id="037f1-636">Add policies from the Polly registry</span></span>

<span data-ttu-id="037f1-637">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="037f1-637">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="037f1-638">An extension method is provided which allows a handler to be added using a policy from the registry:</span><span class="sxs-lookup"><span data-stu-id="037f1-638">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="037f1-639">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="037f1-639">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="037f1-640">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span><span class="sxs-lookup"><span data-stu-id="037f1-640">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="037f1-641">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="037f1-641">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="037f1-642">HttpClient and lifetime management</span><span class="sxs-lookup"><span data-stu-id="037f1-642">HttpClient and lifetime management</span></span>

<span data-ttu-id="037f1-643">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-643">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-644">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span><span class="sxs-lookup"><span data-stu-id="037f1-644">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="037f1-645">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-645">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="037f1-646">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span><span class="sxs-lookup"><span data-stu-id="037f1-646">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="037f1-647">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span><span class="sxs-lookup"><span data-stu-id="037f1-647">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="037f1-648">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span><span class="sxs-lookup"><span data-stu-id="037f1-648">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="037f1-649">Creating more handlers than necessary can result in connection delays.</span><span class="sxs-lookup"><span data-stu-id="037f1-649">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="037f1-650">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span><span class="sxs-lookup"><span data-stu-id="037f1-650">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="037f1-651">The default handler lifetime is two minutes.</span><span class="sxs-lookup"><span data-stu-id="037f1-651">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="037f1-652">The default value can be overridden on a per named client basis.</span><span class="sxs-lookup"><span data-stu-id="037f1-652">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="037f1-653">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span><span class="sxs-lookup"><span data-stu-id="037f1-653">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="037f1-654">Disposal of the client isn't required.</span><span class="sxs-lookup"><span data-stu-id="037f1-654">Disposal of the client isn't required.</span></span> <span data-ttu-id="037f1-655">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="037f1-655">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="037f1-656">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-656">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="037f1-657">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span><span class="sxs-lookup"><span data-stu-id="037f1-657">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="037f1-658">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-658">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="037f1-659">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="037f1-659">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="037f1-660">Alternatives to IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="037f1-660">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="037f1-661">Using `IHttpClientFactory` in a DI-enabled app avoids:</span><span class="sxs-lookup"><span data-stu-id="037f1-661">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="037f1-662">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-662">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="037f1-663">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-663">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="037f1-664">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="037f1-664">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="037f1-665">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span><span class="sxs-lookup"><span data-stu-id="037f1-665">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="037f1-666">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span><span class="sxs-lookup"><span data-stu-id="037f1-666">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="037f1-667">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span><span class="sxs-lookup"><span data-stu-id="037f1-667">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="037f1-668">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span><span class="sxs-lookup"><span data-stu-id="037f1-668">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="037f1-669">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="037f1-669">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="037f1-670">This sharing prevents socket exhaustion.</span><span class="sxs-lookup"><span data-stu-id="037f1-670">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="037f1-671">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span><span class="sxs-lookup"><span data-stu-id="037f1-671">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="037f1-672">Cookies</span><span class="sxs-lookup"><span data-stu-id="037f1-672">Cookies</span></span>

<span data-ttu-id="037f1-673">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span><span class="sxs-lookup"><span data-stu-id="037f1-673">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="037f1-674">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span><span class="sxs-lookup"><span data-stu-id="037f1-674">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="037f1-675">For apps that require cookies, consider either:</span><span class="sxs-lookup"><span data-stu-id="037f1-675">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="037f1-676">Disabling automatic cookie handling</span><span class="sxs-lookup"><span data-stu-id="037f1-676">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="037f1-677">Avoiding `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="037f1-677">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="037f1-678">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span><span class="sxs-lookup"><span data-stu-id="037f1-678">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="037f1-679">protokolování</span><span class="sxs-lookup"><span data-stu-id="037f1-679">Logging</span></span>

<span data-ttu-id="037f1-680">Clients created via `IHttpClientFactory` record log messages for all requests.</span><span class="sxs-lookup"><span data-stu-id="037f1-680">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="037f1-681">Enable the appropriate information level in your logging configuration to see the default log messages.</span><span class="sxs-lookup"><span data-stu-id="037f1-681">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="037f1-682">Additional logging, such as the logging of request headers, is only included at trace level.</span><span class="sxs-lookup"><span data-stu-id="037f1-682">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="037f1-683">The log category used for each client includes the name of the client.</span><span class="sxs-lookup"><span data-stu-id="037f1-683">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="037f1-684">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="037f1-684">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="037f1-685">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-685">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="037f1-686">On the request, messages are logged before any other handlers in the pipeline have processed it.</span><span class="sxs-lookup"><span data-stu-id="037f1-686">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="037f1-687">On the response, messages are logged after any other pipeline handlers have received the response.</span><span class="sxs-lookup"><span data-stu-id="037f1-687">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="037f1-688">Logging also occurs inside the request handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-688">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="037f1-689">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="037f1-689">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="037f1-690">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span><span class="sxs-lookup"><span data-stu-id="037f1-690">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="037f1-691">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span><span class="sxs-lookup"><span data-stu-id="037f1-691">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="037f1-692">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span><span class="sxs-lookup"><span data-stu-id="037f1-692">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="037f1-693">This may include changes to request headers, for example, or to the response status code.</span><span class="sxs-lookup"><span data-stu-id="037f1-693">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="037f1-694">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span><span class="sxs-lookup"><span data-stu-id="037f1-694">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="037f1-695">Configure the HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="037f1-695">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="037f1-696">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span><span class="sxs-lookup"><span data-stu-id="037f1-696">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="037f1-697">An `IHttpClientBuilder` is returned when adding named or typed clients.</span><span class="sxs-lookup"><span data-stu-id="037f1-697">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="037f1-698">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span><span class="sxs-lookup"><span data-stu-id="037f1-698">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="037f1-699">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span><span class="sxs-lookup"><span data-stu-id="037f1-699">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="037f1-700">Use IHttpClientFactory in a console app</span><span class="sxs-lookup"><span data-stu-id="037f1-700">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="037f1-701">In a console app, add the following package references to the project:</span><span class="sxs-lookup"><span data-stu-id="037f1-701">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="037f1-702">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="037f1-702">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="037f1-703">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="037f1-703">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="037f1-704">In the following example:</span><span class="sxs-lookup"><span data-stu-id="037f1-704">In the following example:</span></span>

* <span data-ttu-id="037f1-705"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span><span class="sxs-lookup"><span data-stu-id="037f1-705"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="037f1-706">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="037f1-706">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="037f1-707">`HttpClient` is used to retrieve a webpage.</span><span class="sxs-lookup"><span data-stu-id="037f1-707">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="037f1-708">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span><span class="sxs-lookup"><span data-stu-id="037f1-708">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="037f1-709">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="037f1-709">Additional resources</span></span>

* [<span data-ttu-id="037f1-710">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="037f1-710">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="037f1-711">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span><span class="sxs-lookup"><span data-stu-id="037f1-711">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="037f1-712">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="037f1-712">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
