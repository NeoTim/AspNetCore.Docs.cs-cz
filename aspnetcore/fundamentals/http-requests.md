---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="4c0f2-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c0f2-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4c0f2-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin</span><span class="sxs-lookup"><span data-stu-id="4c0f2-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="4c0f2-105"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="4c0f2-106">`IHttpClientFactory` nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="4c0f2-107">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-108">Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="4c0f2-109">Pro obecný přístup je možné zaregistrovat výchozího klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="4c0f2-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="4c0f2-111">Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="4c0f2-112">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="4c0f2-113">Automatická správa zabraňuje běžným problémům DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient`ch dob života.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="4c0f2-114">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="4c0f2-115">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4c0f2-116">Vzorový kód v této verzi tématu používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="4c0f2-117">V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>`použijte selektor verzí k výběru verze 2. x tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="4c0f2-118">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="4c0f2-118">Consumption patterns</span></span>

<span data-ttu-id="4c0f2-119">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="4c0f2-120">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="4c0f2-121">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="4c0f2-122">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="4c0f2-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="4c0f2-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="4c0f2-125">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-125">Basic usage</span></span>

<span data-ttu-id="4c0f2-126">`IHttpClientFactory` lze zaregistrovat voláním `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c0f2-127">`IHttpClientFactory` lze požadovat pomocí [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4c0f2-128">Následující kód používá `IHttpClientFactory` k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="4c0f2-129">Použití `IHttpClientFactory` jako v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="4c0f2-130">Nemá žádný vliv na to, jak se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="4c0f2-131">V místech, kde jsou `HttpClient` instance vytvořené v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="4c0f2-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-132">Named clients</span></span>

<span data-ttu-id="4c0f2-133">Pojmenovaná klienti jsou dobrou volbou v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="4c0f2-134">Aplikace vyžaduje mnoho různých použití `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="4c0f2-135">Mnoho `HttpClient`s má odlišnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="4c0f2-136">Konfigurace pro pojmenovanou `HttpClient` se dá zadat během registrace v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="4c0f2-137">V předchozím kódu je klient nakonfigurován pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="4c0f2-138">Základní adresa `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="4c0f2-139">Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="4c0f2-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="4c0f2-140">CreateClient</span></span>

<span data-ttu-id="4c0f2-141">Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*>, je volána:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="4c0f2-142">Vytvoří se nová instance `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="4c0f2-143">Je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-143">The configuration action is called.</span></span>

<span data-ttu-id="4c0f2-144">Chcete-li vytvořit pojmenovaného klienta, předejte jeho název do `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="4c0f2-145">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="4c0f2-146">Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="4c0f2-147">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-147">Typed clients</span></span>

<span data-ttu-id="4c0f2-148">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-148">Typed clients:</span></span>

* <span data-ttu-id="4c0f2-149">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="4c0f2-150">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="4c0f2-151">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="4c0f2-152">Můžete například použít jednoho typu klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="4c0f2-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="4c0f2-154">Zapouzdřit veškerou práci s koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="4c0f2-155">Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="4c0f2-156">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4c0f2-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-157">In the preceding code:</span></span>

* <span data-ttu-id="4c0f2-158">Konfigurace je přesunuta do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="4c0f2-159">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="4c0f2-160">Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="4c0f2-161">Například metoda `GetAspNetDocsIssues` zapouzdřuje kód, aby načetla otevřené problémy.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="4c0f2-162">Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="4c0f2-163">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="4c0f2-164">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="4c0f2-165">Konfigurace pro zadaného klienta se dá zadat během registrace v `Startup.ConfigureServices`místo v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="4c0f2-166">`HttpClient` lze zapouzdřit v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="4c0f2-167">Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která interně volá instanci `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="4c0f2-168">V předchozím kódu je `HttpClient` uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="4c0f2-169">Přístup k `HttpClient` je veřejným `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="4c0f2-170">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-170">Generated clients</span></span>

<span data-ttu-id="4c0f2-171">`IHttpClientFactory` lze použít v kombinaci s knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="4c0f2-172">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="4c0f2-173">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="4c0f2-174">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="4c0f2-175">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="4c0f2-176">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="4c0f2-177">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="4c0f2-178">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="4c0f2-178">Outgoing request middleware</span></span>

<span data-ttu-id="4c0f2-179">`HttpClient` má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="4c0f2-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="4c0f2-181">Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="4c0f2-182">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="4c0f2-183">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="4c0f2-184">Tento model:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-184">This pattern:</span></span>

  * <span data-ttu-id="4c0f2-185">Se podobá příchozímu kanálu middleware v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="4c0f2-186">Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="4c0f2-187">ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-187">caching</span></span>
    * <span data-ttu-id="4c0f2-188">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="4c0f2-188">error handling</span></span>
    * <span data-ttu-id="4c0f2-189">serializace</span><span class="sxs-lookup"><span data-stu-id="4c0f2-189">serialization</span></span>
    * <span data-ttu-id="4c0f2-190">protokolování</span><span class="sxs-lookup"><span data-stu-id="4c0f2-190">logging</span></span>

<span data-ttu-id="4c0f2-191">Vytvoření obslužné rutiny delegování:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-191">To create a delegating handler:</span></span>

* <span data-ttu-id="4c0f2-192">Odvodit z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="4c0f2-193">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="4c0f2-194">Před předáním požadavku další obslužné rutině v kanálu spusťte kód:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="4c0f2-195">Předchozí kód zkontroluje, zda je hlavička `X-API-KEY` v žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="4c0f2-196">Pokud chybí `X-API-KEY`, vrátí se <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="4c0f2-197">Do konfigurace pro `HttpClient` se dá přidat víc než jedna obslužná rutina s <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="4c0f2-198">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="4c0f2-199">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="4c0f2-200">Obslužné rutiny mohou záviset na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="4c0f2-201">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="4c0f2-202">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="4c0f2-203">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="4c0f2-204">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="4c0f2-205">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="4c0f2-206">Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="4c0f2-207">Pro přístup k aktuálnímu požadavku použijte <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="4c0f2-208">Vytvořte vlastní objekt úložiště <xref:System.Threading.AsyncLocal`1>, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="4c0f2-209">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-209">Use Polly-based handlers</span></span>

<span data-ttu-id="4c0f2-210">`IHttpClientFactory` se integruje s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="4c0f2-211">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="4c0f2-212">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="4c0f2-213">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-214">Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="4c0f2-215">Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="4c0f2-216">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="4c0f2-216">Handle transient faults</span></span>

<span data-ttu-id="4c0f2-217">K chybám obvykle dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="4c0f2-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="4c0f2-219">Zásady nakonfigurované pomocí `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="4c0f2-220">5xx HTTP</span><span class="sxs-lookup"><span data-stu-id="4c0f2-220">HTTP 5xx</span></span>
* <span data-ttu-id="4c0f2-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="4c0f2-221">HTTP 408</span></span>

<span data-ttu-id="4c0f2-222">`AddTransientHttpErrorPolicy` poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="4c0f2-223">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="4c0f2-224">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="4c0f2-225">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="4c0f2-225">Dynamically select policies</span></span>

<span data-ttu-id="4c0f2-226">Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="4c0f2-227">Následující `AddPolicyHandler` Overload zkontroluje požadavek na rozhodnutí, které zásady se mají použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="4c0f2-228">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="4c0f2-229">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="4c0f2-230">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="4c0f2-231">Je běžné vnořovat zásady Polly:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="4c0f2-232">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-232">In the preceding example:</span></span>

* <span data-ttu-id="4c0f2-233">Přidávají se dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-233">Two handlers are added.</span></span>
* <span data-ttu-id="4c0f2-234">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="4c0f2-235">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="4c0f2-236">Druhý `AddTransientHttpErrorPolicy` volání přidá zásadu dělení na okruhy.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="4c0f2-237">Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="4c0f2-238">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="4c0f2-239">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="4c0f2-240">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="4c0f2-241">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="4c0f2-242">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-242">In the following code:</span></span>

* <span data-ttu-id="4c0f2-243">Přidávají se zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="4c0f2-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="4c0f2-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> přidá do registru zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="4c0f2-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="4c0f2-245">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="4c0f2-246">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="4c0f2-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="4c0f2-247">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-248">Vytvoří se <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="4c0f2-249">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="4c0f2-250">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="4c0f2-251">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="4c0f2-252">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="4c0f2-253">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="4c0f2-254">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="4c0f2-255">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="4c0f2-256">Výchozí hodnota může být přepsána podle pojmenovaných klientů:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="4c0f2-257">instance `HttpClient` můžou být obecně ošetřené **jako objekty .NET, které** nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="4c0f2-258">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="4c0f2-259">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="4c0f2-260">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-261">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="4c0f2-262">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="4c0f2-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="4c0f2-263">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="4c0f2-264">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="4c0f2-265">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných instancích.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-265">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="4c0f2-266">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="4c0f2-267">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="4c0f2-268">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="4c0f2-269">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-269">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="4c0f2-270">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="4c0f2-271">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-272">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="4c0f2-273">`SocketsHttpHandler ` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo problémům se stavem služby DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-273">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="4c0f2-274">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-274">Cookies</span></span>

<span data-ttu-id="4c0f2-275">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="4c0f2-276">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="4c0f2-277">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="4c0f2-278">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="4c0f2-279">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="4c0f2-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="4c0f2-280">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="4c0f2-281">Protokolování</span><span class="sxs-lookup"><span data-stu-id="4c0f2-281">Logging</span></span>

<span data-ttu-id="4c0f2-282">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="4c0f2-283">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="4c0f2-284">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="4c0f2-285">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="4c0f2-286">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="4c0f2-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="4c0f2-287">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-288">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="4c0f2-289">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="4c0f2-290">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-291">V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="4c0f2-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="4c0f2-292">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="4c0f2-293">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="4c0f2-294">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="4c0f2-295">To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="4c0f2-296">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="4c0f2-297">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="4c0f2-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="4c0f2-298">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="4c0f2-299">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="4c0f2-300">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="4c0f2-301">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="4c0f2-302">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="4c0f2-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="4c0f2-303">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="4c0f2-304">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="4c0f2-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="4c0f2-305">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="4c0f2-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="4c0f2-306">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-306">In the following example:</span></span>

* <span data-ttu-id="4c0f2-307"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="4c0f2-308">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="4c0f2-309">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="4c0f2-310">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="4c0f2-311">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-311">Additional resources</span></span>

* [<span data-ttu-id="4c0f2-312">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="4c0f2-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="4c0f2-313">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="4c0f2-314">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="4c0f2-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4c0f2-315">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="4c0f2-315">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="4c0f2-316"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-316">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="4c0f2-317">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-317">It offers the following benefits:</span></span>

* <span data-ttu-id="4c0f2-318">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-318">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-319">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-319">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="4c0f2-320">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-320">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="4c0f2-321">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-321">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="4c0f2-322">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-322">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="4c0f2-323">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-323">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="4c0f2-324">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c0f2-324">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="4c0f2-325">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="4c0f2-325">Consumption patterns</span></span>

<span data-ttu-id="4c0f2-326">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-326">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="4c0f2-327">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-327">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="4c0f2-328">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-328">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="4c0f2-329">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-329">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="4c0f2-330">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-330">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="4c0f2-331">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-331">None of them are strictly superior to another.</span></span> <span data-ttu-id="4c0f2-332">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-332">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="4c0f2-333">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-333">Basic usage</span></span>

<span data-ttu-id="4c0f2-334">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-334">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c0f2-335">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-335">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4c0f2-336">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-336">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="4c0f2-337">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-337">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="4c0f2-338">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-338">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="4c0f2-339">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-339">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="4c0f2-340">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-340">Named clients</span></span>

<span data-ttu-id="4c0f2-341">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-341">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="4c0f2-342">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-342">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="4c0f2-343">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-343">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="4c0f2-344">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-344">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="4c0f2-345">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-345">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="4c0f2-346">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-346">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="4c0f2-347">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-347">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="4c0f2-348">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-348">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="4c0f2-349">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-349">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="4c0f2-350">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-350">Typed clients</span></span>

<span data-ttu-id="4c0f2-351">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-351">Typed clients:</span></span>

* <span data-ttu-id="4c0f2-352">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-352">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="4c0f2-353">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-353">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="4c0f2-354">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-354">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="4c0f2-355">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-355">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="4c0f2-356">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-356">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="4c0f2-357">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-357">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4c0f2-358">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-358">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="4c0f2-359">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-359">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="4c0f2-360">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-360">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="4c0f2-361">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-361">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="4c0f2-362">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-362">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="4c0f2-363">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-363">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="4c0f2-364">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-364">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="4c0f2-365">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-365">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="4c0f2-366">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-366">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="4c0f2-367">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-367">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="4c0f2-368">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-368">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="4c0f2-369">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-369">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="4c0f2-370">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-370">Generated clients</span></span>

<span data-ttu-id="4c0f2-371">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-371">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="4c0f2-372">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-372">Refit is a REST library for .NET.</span></span> <span data-ttu-id="4c0f2-373">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-373">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="4c0f2-374">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-374">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="4c0f2-375">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-375">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="4c0f2-376">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-376">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="4c0f2-377">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-377">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="4c0f2-378">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="4c0f2-378">Outgoing request middleware</span></span>

<span data-ttu-id="4c0f2-379">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-379">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="4c0f2-380">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-380">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="4c0f2-381">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-381">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="4c0f2-382">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-382">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="4c0f2-383">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-383">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="4c0f2-384">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-384">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="4c0f2-385">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-385">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="4c0f2-386">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-386">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="4c0f2-387">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-387">The preceding code defines a basic handler.</span></span> <span data-ttu-id="4c0f2-388">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-388">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="4c0f2-389">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-389">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="4c0f2-390">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-390">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="4c0f2-391">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-391">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="4c0f2-392">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-392">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="4c0f2-393">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-393">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="4c0f2-394">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-394">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="4c0f2-395">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-395">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="4c0f2-396">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-396">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="4c0f2-397">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-397">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="4c0f2-398">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-398">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="4c0f2-399">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-399">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="4c0f2-400">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-400">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="4c0f2-401">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-401">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="4c0f2-402">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-402">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="4c0f2-403">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-403">Use Polly-based handlers</span></span>

<span data-ttu-id="4c0f2-404">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-404">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="4c0f2-405">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-405">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="4c0f2-406">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-406">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="4c0f2-407">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-407">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-408">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-408">The Polly extensions:</span></span>

* <span data-ttu-id="4c0f2-409">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-409">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="4c0f2-410">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-410">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="4c0f2-411">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-411">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="4c0f2-412">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="4c0f2-412">Handle transient faults</span></span>

<span data-ttu-id="4c0f2-413">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-413">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="4c0f2-414">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-414">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="4c0f2-415">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-415">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="4c0f2-416">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-416">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4c0f2-417">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-417">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="4c0f2-418">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-418">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="4c0f2-419">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-419">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="4c0f2-420">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="4c0f2-420">Dynamically select policies</span></span>

<span data-ttu-id="4c0f2-421">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-421">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="4c0f2-422">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-422">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="4c0f2-423">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-423">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="4c0f2-424">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-424">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="4c0f2-425">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-425">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="4c0f2-426">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-426">Add multiple Polly handlers</span></span>

<span data-ttu-id="4c0f2-427">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-427">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="4c0f2-428">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-428">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="4c0f2-429">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-429">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="4c0f2-430">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-430">Failed requests are retried up to three times.</span></span> <span data-ttu-id="4c0f2-431">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-431">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="4c0f2-432">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-432">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="4c0f2-433">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-433">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="4c0f2-434">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-434">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="4c0f2-435">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-435">Add policies from the Polly registry</span></span>

<span data-ttu-id="4c0f2-436">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-436">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="4c0f2-437">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-437">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="4c0f2-438">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-438">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="4c0f2-439">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-439">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="4c0f2-440">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-440">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="4c0f2-441">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="4c0f2-441">HttpClient and lifetime management</span></span>

<span data-ttu-id="4c0f2-442">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-442">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-443">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-443">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="4c0f2-444">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-444">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="4c0f2-445">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-445">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="4c0f2-446">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-446">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="4c0f2-447">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-447">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="4c0f2-448">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-448">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="4c0f2-449">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-449">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="4c0f2-450">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-450">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="4c0f2-451">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-451">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="4c0f2-452">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-452">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="4c0f2-453">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-453">Disposal of the client isn't required.</span></span> <span data-ttu-id="4c0f2-454">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-454">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="4c0f2-455">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-455">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-456">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-456">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="4c0f2-457">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-457">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-458">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-458">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="4c0f2-459">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="4c0f2-459">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="4c0f2-460">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-460">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="4c0f2-461">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-461">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="4c0f2-462">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných instancích.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-462">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="4c0f2-463">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-463">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="4c0f2-464">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-464">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="4c0f2-465">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-465">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="4c0f2-466">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-466">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="4c0f2-467">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-467">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="4c0f2-468">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-468">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-469">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-469">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="4c0f2-470">`SocketsHttpHandler ` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo problémům se stavem služby DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-470">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="4c0f2-471">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-471">Cookies</span></span>

<span data-ttu-id="4c0f2-472">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-472">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="4c0f2-473">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-473">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="4c0f2-474">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-474">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="4c0f2-475">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-475">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="4c0f2-476">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="4c0f2-476">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="4c0f2-477">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-477">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="4c0f2-478">Protokolování</span><span class="sxs-lookup"><span data-stu-id="4c0f2-478">Logging</span></span>

<span data-ttu-id="4c0f2-479">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-479">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="4c0f2-480">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-480">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="4c0f2-481">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-481">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="4c0f2-482">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-482">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="4c0f2-483">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-483">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="4c0f2-484">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-484">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-485">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-485">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="4c0f2-486">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-486">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="4c0f2-487">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-487">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-488">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-488">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="4c0f2-489">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-489">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="4c0f2-490">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-490">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="4c0f2-491">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-491">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="4c0f2-492">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-492">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="4c0f2-493">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-493">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="4c0f2-494">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="4c0f2-494">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="4c0f2-495">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-495">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="4c0f2-496">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-496">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="4c0f2-497">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-497">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="4c0f2-498">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-498">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="4c0f2-499">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="4c0f2-499">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="4c0f2-500">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-500">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="4c0f2-501">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="4c0f2-501">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="4c0f2-502">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="4c0f2-502">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="4c0f2-503">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-503">In the following example:</span></span>

* <span data-ttu-id="4c0f2-504"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-504"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="4c0f2-505">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-505">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="4c0f2-506">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-506">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="4c0f2-507">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-507">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="4c0f2-508">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-508">Additional resources</span></span>

* [<span data-ttu-id="4c0f2-509">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="4c0f2-509">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="4c0f2-510">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-510">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="4c0f2-511">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="4c0f2-511">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="4c0f2-512">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="4c0f2-512">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="4c0f2-513"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-513">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="4c0f2-514">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-514">It offers the following benefits:</span></span>

* <span data-ttu-id="4c0f2-515">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-515">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-516">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-516">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="4c0f2-517">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-517">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="4c0f2-518">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-518">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="4c0f2-519">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-519">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="4c0f2-520">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-520">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="4c0f2-521">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c0f2-521">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c0f2-522">Požadavky</span><span class="sxs-lookup"><span data-stu-id="4c0f2-522">Prerequisites</span></span>

<span data-ttu-id="4c0f2-523">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-523">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="4c0f2-524">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už obsahují balíček `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-524">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="4c0f2-525">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="4c0f2-525">Consumption patterns</span></span>

<span data-ttu-id="4c0f2-526">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-526">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="4c0f2-527">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-527">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="4c0f2-528">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-528">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="4c0f2-529">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-529">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="4c0f2-530">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-530">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="4c0f2-531">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-531">None of them are strictly superior to another.</span></span> <span data-ttu-id="4c0f2-532">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-532">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="4c0f2-533">Základní využití</span><span class="sxs-lookup"><span data-stu-id="4c0f2-533">Basic usage</span></span>

<span data-ttu-id="4c0f2-534">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-534">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c0f2-535">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-535">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4c0f2-536">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-536">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="4c0f2-537">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-537">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="4c0f2-538">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-538">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="4c0f2-539">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-539">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="4c0f2-540">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-540">Named clients</span></span>

<span data-ttu-id="4c0f2-541">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-541">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="4c0f2-542">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-542">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="4c0f2-543">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-543">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="4c0f2-544">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-544">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="4c0f2-545">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-545">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="4c0f2-546">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-546">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="4c0f2-547">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-547">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="4c0f2-548">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-548">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="4c0f2-549">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-549">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="4c0f2-550">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="4c0f2-550">Typed clients</span></span>

<span data-ttu-id="4c0f2-551">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-551">Typed clients:</span></span>

* <span data-ttu-id="4c0f2-552">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-552">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="4c0f2-553">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-553">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="4c0f2-554">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-554">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="4c0f2-555">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-555">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="4c0f2-556">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-556">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="4c0f2-557">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-557">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="4c0f2-558">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-558">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="4c0f2-559">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-559">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="4c0f2-560">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-560">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="4c0f2-561">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-561">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="4c0f2-562">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-562">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="4c0f2-563">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-563">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="4c0f2-564">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-564">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="4c0f2-565">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-565">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="4c0f2-566">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-566">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="4c0f2-567">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-567">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="4c0f2-568">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-568">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="4c0f2-569">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-569">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="4c0f2-570">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="4c0f2-570">Generated clients</span></span>

<span data-ttu-id="4c0f2-571">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-571">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="4c0f2-572">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-572">Refit is a REST library for .NET.</span></span> <span data-ttu-id="4c0f2-573">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-573">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="4c0f2-574">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-574">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="4c0f2-575">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-575">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="4c0f2-576">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-576">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="4c0f2-577">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-577">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="4c0f2-578">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="4c0f2-578">Outgoing request middleware</span></span>

<span data-ttu-id="4c0f2-579">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-579">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="4c0f2-580">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-580">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="4c0f2-581">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-581">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="4c0f2-582">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-582">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="4c0f2-583">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-583">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="4c0f2-584">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-584">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="4c0f2-585">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-585">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="4c0f2-586">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-586">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="4c0f2-587">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-587">The preceding code defines a basic handler.</span></span> <span data-ttu-id="4c0f2-588">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-588">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="4c0f2-589">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-589">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="4c0f2-590">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-590">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="4c0f2-591">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-591">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="4c0f2-592">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-592">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="4c0f2-593">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-593">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="4c0f2-594">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-594">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="4c0f2-595">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-595">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="4c0f2-596">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-596">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="4c0f2-597">Po zaregistrování můžete <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> volat a předat do něj typ obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-597">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="4c0f2-598">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-598">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="4c0f2-599">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-599">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="4c0f2-600">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-600">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="4c0f2-601">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-601">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="4c0f2-602">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-602">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="4c0f2-603">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-603">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="4c0f2-604">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-604">Use Polly-based handlers</span></span>

<span data-ttu-id="4c0f2-605">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-605">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="4c0f2-606">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-606">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="4c0f2-607">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-607">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="4c0f2-608">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-608">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-609">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-609">The Polly extensions:</span></span>

* <span data-ttu-id="4c0f2-610">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-610">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="4c0f2-611">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-611">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="4c0f2-612">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-612">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="4c0f2-613">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="4c0f2-613">Handle transient faults</span></span>

<span data-ttu-id="4c0f2-614">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-614">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="4c0f2-615">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-615">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="4c0f2-616">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-616">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="4c0f2-617">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-617">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4c0f2-618">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-618">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="4c0f2-619">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-619">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="4c0f2-620">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-620">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="4c0f2-621">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="4c0f2-621">Dynamically select policies</span></span>

<span data-ttu-id="4c0f2-622">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-622">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="4c0f2-623">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-623">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="4c0f2-624">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-624">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="4c0f2-625">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-625">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="4c0f2-626">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-626">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="4c0f2-627">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-627">Add multiple Polly handlers</span></span>

<span data-ttu-id="4c0f2-628">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-628">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="4c0f2-629">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-629">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="4c0f2-630">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-630">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="4c0f2-631">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-631">Failed requests are retried up to three times.</span></span> <span data-ttu-id="4c0f2-632">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-632">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="4c0f2-633">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-633">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="4c0f2-634">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-634">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="4c0f2-635">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-635">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="4c0f2-636">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-636">Add policies from the Polly registry</span></span>

<span data-ttu-id="4c0f2-637">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-637">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="4c0f2-638">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-638">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="4c0f2-639">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-639">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="4c0f2-640">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-640">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="4c0f2-641">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="4c0f2-641">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="4c0f2-642">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="4c0f2-642">HttpClient and lifetime management</span></span>

<span data-ttu-id="4c0f2-643">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-643">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-644">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-644">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="4c0f2-645">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-645">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="4c0f2-646">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-646">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="4c0f2-647">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-647">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="4c0f2-648">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-648">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="4c0f2-649">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-649">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="4c0f2-650">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-650">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="4c0f2-651">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-651">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="4c0f2-652">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-652">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="4c0f2-653">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-653">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="4c0f2-654">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-654">Disposal of the client isn't required.</span></span> <span data-ttu-id="4c0f2-655">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-655">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="4c0f2-656">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-656">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-657">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-657">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="4c0f2-658">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-658">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="4c0f2-659">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-659">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="4c0f2-660">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="4c0f2-660">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="4c0f2-661">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-661">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="4c0f2-662">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-662">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="4c0f2-663">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných instancích.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-663">Stale-DNS problems by cycling `HttpMessageHandler` instances at regular instances.</span></span>

<span data-ttu-id="4c0f2-664">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-664">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="4c0f2-665">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-665">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="4c0f2-666">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-666">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="4c0f2-667">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-667">Create `HttpClient` instances using `new HttpClient(handler, dispostHandler: false)` as needed.</span></span>

<span data-ttu-id="4c0f2-668">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-668">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="4c0f2-669">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-669">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="4c0f2-670">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-670">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="4c0f2-671">`SocketsHttpHandler ` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo problémům se stavem služby DNS.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-671">The `SocketsHttpHandler ` cycles connections according to `PooledConnectionLifetime` to avoid state-DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="4c0f2-672">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-672">Cookies</span></span>

<span data-ttu-id="4c0f2-673">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-673">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="4c0f2-674">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-674">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="4c0f2-675">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-675">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="4c0f2-676">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="4c0f2-676">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="4c0f2-677">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="4c0f2-677">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="4c0f2-678">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-678">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="4c0f2-679">Protokolování</span><span class="sxs-lookup"><span data-stu-id="4c0f2-679">Logging</span></span>

<span data-ttu-id="4c0f2-680">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-680">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="4c0f2-681">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-681">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="4c0f2-682">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-682">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="4c0f2-683">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-683">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="4c0f2-684">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-684">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="4c0f2-685">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-685">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-686">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-686">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="4c0f2-687">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-687">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="4c0f2-688">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-688">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="4c0f2-689">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-689">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="4c0f2-690">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-690">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="4c0f2-691">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-691">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="4c0f2-692">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-692">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="4c0f2-693">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-693">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="4c0f2-694">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-694">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="4c0f2-695">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="4c0f2-695">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="4c0f2-696">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-696">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="4c0f2-697">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-697">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="4c0f2-698">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-698">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="4c0f2-699">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-699">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="4c0f2-700">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="4c0f2-700">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="4c0f2-701">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-701">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="4c0f2-702">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="4c0f2-702">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="4c0f2-703">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="4c0f2-703">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="4c0f2-704">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-704">In the following example:</span></span>

* <span data-ttu-id="4c0f2-705"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="4c0f2-705"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="4c0f2-706">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-706">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="4c0f2-707">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-707">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="4c0f2-708">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="4c0f2-708">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="4c0f2-709">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="4c0f2-709">Additional resources</span></span>

* [<span data-ttu-id="4c0f2-710">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="4c0f2-710">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="4c0f2-711">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="4c0f2-711">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="4c0f2-712">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="4c0f2-712">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
