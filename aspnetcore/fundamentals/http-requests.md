---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: a963833acfa12889c8ae3dac443962682e1cb931
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190580"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="bb8f0-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bb8f0-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb8f0-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin</span><span class="sxs-lookup"><span data-stu-id="bb8f0-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="bb8f0-105"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bb8f0-106">`IHttpClientFactory` nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="bb8f0-107">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-108">Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bb8f0-109">Pro obecný přístup je možné zaregistrovat výchozího klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="bb8f0-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="bb8f0-111">Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="bb8f0-112">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="bb8f0-113">Automatická správa zabraňuje běžným problémům DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient`ch dob života.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bb8f0-114">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bb8f0-115">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="bb8f0-116">Vzorový kód v této verzi tématu používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="bb8f0-117">V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>`použijte selektor verzí k výběru verze 2. x tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bb8f0-118">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="bb8f0-118">Consumption patterns</span></span>

<span data-ttu-id="bb8f0-119">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bb8f0-120">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bb8f0-121">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bb8f0-122">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bb8f0-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bb8f0-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bb8f0-125">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-125">Basic usage</span></span>

<span data-ttu-id="bb8f0-126">`IHttpClientFactory` lze zaregistrovat voláním `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bb8f0-127">`IHttpClientFactory` lze požadovat pomocí [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bb8f0-128">Následující kód používá `IHttpClientFactory` k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bb8f0-129">Použití `IHttpClientFactory` jako v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="bb8f0-130">Nemá žádný vliv na to, jak se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="bb8f0-131">V místech, kde jsou `HttpClient` instance vytvořené v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bb8f0-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-132">Named clients</span></span>

<span data-ttu-id="bb8f0-133">Pojmenovaná klienti jsou dobrou volbou v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="bb8f0-134">Aplikace vyžaduje mnoho různých použití `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="bb8f0-135">Mnoho `HttpClient`s má odlišnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="bb8f0-136">Konfigurace pro pojmenovanou `HttpClient` se dá zadat během registrace v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bb8f0-137">V předchozím kódu je klient nakonfigurován pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="bb8f0-138">Základní adresa `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="bb8f0-139">Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="bb8f0-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="bb8f0-140">CreateClient</span></span>

<span data-ttu-id="bb8f0-141">Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*>, je volána:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="bb8f0-142">Vytvoří se nová instance `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="bb8f0-143">Je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-143">The configuration action is called.</span></span>

<span data-ttu-id="bb8f0-144">Chcete-li vytvořit pojmenovaného klienta, předejte jeho název do `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bb8f0-145">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bb8f0-146">Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bb8f0-147">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-147">Typed clients</span></span>

<span data-ttu-id="bb8f0-148">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-148">Typed clients:</span></span>

* <span data-ttu-id="bb8f0-149">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bb8f0-150">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bb8f0-151">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bb8f0-152">Můžete například použít jednoho typu klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="bb8f0-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="bb8f0-154">Zapouzdřit veškerou práci s koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="bb8f0-155">Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="bb8f0-156">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bb8f0-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-157">In the preceding code:</span></span>

* <span data-ttu-id="bb8f0-158">Konfigurace je přesunuta do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="bb8f0-159">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="bb8f0-160">Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bb8f0-161">Například metoda `GetAspNetDocsIssues` zapouzdřuje kód, aby načetla otevřené problémy.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="bb8f0-162">Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bb8f0-163">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bb8f0-164">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bb8f0-165">Konfigurace pro zadaného klienta se dá zadat během registrace v `Startup.ConfigureServices`místo v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bb8f0-166">`HttpClient` lze zapouzdřit v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="bb8f0-167">Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která interně volá instanci `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bb8f0-168">V předchozím kódu je `HttpClient` uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="bb8f0-169">Přístup k `HttpClient` je veřejným `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bb8f0-170">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-170">Generated clients</span></span>

<span data-ttu-id="bb8f0-171">`IHttpClientFactory` lze použít v kombinaci s knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bb8f0-172">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bb8f0-173">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bb8f0-174">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bb8f0-175">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bb8f0-176">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bb8f0-177">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bb8f0-178">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="bb8f0-178">Outgoing request middleware</span></span>

<span data-ttu-id="bb8f0-179">`HttpClient` má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bb8f0-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="bb8f0-181">Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="bb8f0-182">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bb8f0-183">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bb8f0-184">Tento model:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-184">This pattern:</span></span>

  * <span data-ttu-id="bb8f0-185">Se podobá příchozímu kanálu middleware v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="bb8f0-186">Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="bb8f0-187">ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-187">caching</span></span>
    * <span data-ttu-id="bb8f0-188">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="bb8f0-188">error handling</span></span>
    * <span data-ttu-id="bb8f0-189">serializace</span><span class="sxs-lookup"><span data-stu-id="bb8f0-189">serialization</span></span>
    * <span data-ttu-id="bb8f0-190">protokolování</span><span class="sxs-lookup"><span data-stu-id="bb8f0-190">logging</span></span>

<span data-ttu-id="bb8f0-191">Vytvoření obslužné rutiny delegování:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-191">To create a delegating handler:</span></span>

* <span data-ttu-id="bb8f0-192">Odvodit z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="bb8f0-193">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="bb8f0-194">Před předáním požadavku další obslužné rutině v kanálu spusťte kód:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bb8f0-195">Předchozí kód zkontroluje, zda je hlavička `X-API-KEY` v žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="bb8f0-196">Pokud chybí `X-API-KEY`, vrátí se <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="bb8f0-197">Do konfigurace pro `HttpClient` se dá přidat víc než jedna obslužná rutina s <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="bb8f0-198">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bb8f0-199">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="bb8f0-200">Obslužné rutiny mohou záviset na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="bb8f0-201">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="bb8f0-202">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="bb8f0-203">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bb8f0-204">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bb8f0-205">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bb8f0-206">Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="bb8f0-207">Pro přístup k aktuálnímu požadavku použijte <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="bb8f0-208">Vytvořte vlastní objekt úložiště <xref:System.Threading.AsyncLocal`1>, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bb8f0-209">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-209">Use Polly-based handlers</span></span>

<span data-ttu-id="bb8f0-210">`IHttpClientFactory` se integruje s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bb8f0-211">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bb8f0-212">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bb8f0-213">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-214">Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="bb8f0-215">Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bb8f0-216">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="bb8f0-216">Handle transient faults</span></span>

<span data-ttu-id="bb8f0-217">K chybám obvykle dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bb8f0-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bb8f0-219">Zásady nakonfigurované pomocí `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="bb8f0-220">5xx HTTP</span><span class="sxs-lookup"><span data-stu-id="bb8f0-220">HTTP 5xx</span></span>
* <span data-ttu-id="bb8f0-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="bb8f0-221">HTTP 408</span></span>

<span data-ttu-id="bb8f0-222">`AddTransientHttpErrorPolicy` poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="bb8f0-223">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bb8f0-224">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bb8f0-225">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="bb8f0-225">Dynamically select policies</span></span>

<span data-ttu-id="bb8f0-226">Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="bb8f0-227">Následující `AddPolicyHandler` Overload zkontroluje požadavek na rozhodnutí, které zásady se mají použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bb8f0-228">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bb8f0-229">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bb8f0-230">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="bb8f0-231">Je běžné vnořovat zásady Polly:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bb8f0-232">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-232">In the preceding example:</span></span>

* <span data-ttu-id="bb8f0-233">Přidávají se dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-233">Two handlers are added.</span></span>
* <span data-ttu-id="bb8f0-234">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="bb8f0-235">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="bb8f0-236">Druhý `AddTransientHttpErrorPolicy` volání přidá zásadu dělení na okruhy.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="bb8f0-237">Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="bb8f0-238">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bb8f0-239">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bb8f0-240">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="bb8f0-241">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="bb8f0-242">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-242">In the following code:</span></span>

* <span data-ttu-id="bb8f0-243">Přidávají se zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="bb8f0-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="bb8f0-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> přidá do registru zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="bb8f0-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="bb8f0-245">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bb8f0-246">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="bb8f0-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="bb8f0-247">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-248">Vytvoří se <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="bb8f0-249">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bb8f0-250">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bb8f0-251">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bb8f0-252">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bb8f0-253">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bb8f0-254">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="bb8f0-255">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bb8f0-256">Výchozí hodnota může být přepsána podle pojmenovaných klientů:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="bb8f0-257">instance `HttpClient` můžou být obecně ošetřené **jako objekty .NET, které** nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="bb8f0-258">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bb8f0-259">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="bb8f0-260">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-261">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="bb8f0-262">protokolování</span><span class="sxs-lookup"><span data-stu-id="bb8f0-262">Logging</span></span>

<span data-ttu-id="bb8f0-263">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-263">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bb8f0-264">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-264">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="bb8f0-265">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-265">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bb8f0-266">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-266">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bb8f0-267">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="bb8f0-267">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="bb8f0-268">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-268">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-269">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-269">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bb8f0-270">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-270">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bb8f0-271">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-271">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-272">V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="bb8f0-272">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="bb8f0-273">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-273">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="bb8f0-274">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-274">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bb8f0-275">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-275">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bb8f0-276">To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-276">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="bb8f0-277">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-277">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bb8f0-278">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bb8f0-278">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bb8f0-279">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-279">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bb8f0-280">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-280">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bb8f0-281">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-281">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bb8f0-282">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-282">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bb8f0-283">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="bb8f0-283">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bb8f0-284">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-284">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bb8f0-285">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="bb8f0-285">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bb8f0-286">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="bb8f0-286">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bb8f0-287">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-287">In the following example:</span></span>

* <span data-ttu-id="bb8f0-288"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-288"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bb8f0-289">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-289">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bb8f0-290">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-290">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bb8f0-291">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-291">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="bb8f0-292">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bb8f0-292">Additional resources</span></span>

* [<span data-ttu-id="bb8f0-293">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="bb8f0-293">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bb8f0-294">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-294">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bb8f0-295">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="bb8f0-295">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bb8f0-296">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="bb8f0-296">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="bb8f0-297"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-297">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bb8f0-298">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-298">It offers the following benefits:</span></span>

* <span data-ttu-id="bb8f0-299">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-299">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-300">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-300">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bb8f0-301">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-301">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="bb8f0-302">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-302">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="bb8f0-303">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-303">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bb8f0-304">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-304">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bb8f0-305">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb8f0-305">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bb8f0-306">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="bb8f0-306">Consumption patterns</span></span>

<span data-ttu-id="bb8f0-307">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-307">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bb8f0-308">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-308">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bb8f0-309">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-309">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bb8f0-310">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-310">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bb8f0-311">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-311">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bb8f0-312">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-312">None of them are strictly superior to another.</span></span> <span data-ttu-id="bb8f0-313">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-313">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bb8f0-314">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-314">Basic usage</span></span>

<span data-ttu-id="bb8f0-315">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-315">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bb8f0-316">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-316">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bb8f0-317">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-317">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bb8f0-318">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-318">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="bb8f0-319">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-319">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="bb8f0-320">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-320">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bb8f0-321">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-321">Named clients</span></span>

<span data-ttu-id="bb8f0-322">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-322">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="bb8f0-323">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-323">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bb8f0-324">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-324">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="bb8f0-325">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-325">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="bb8f0-326">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-326">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="bb8f0-327">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-327">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="bb8f0-328">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-328">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bb8f0-329">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-329">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bb8f0-330">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-330">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bb8f0-331">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-331">Typed clients</span></span>

<span data-ttu-id="bb8f0-332">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-332">Typed clients:</span></span>

* <span data-ttu-id="bb8f0-333">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-333">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bb8f0-334">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-334">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bb8f0-335">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-335">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bb8f0-336">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-336">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="bb8f0-337">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-337">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="bb8f0-338">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-338">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bb8f0-339">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-339">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="bb8f0-340">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-340">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="bb8f0-341">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-341">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bb8f0-342">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-342">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="bb8f0-343">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-343">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bb8f0-344">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-344">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bb8f0-345">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-345">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bb8f0-346">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-346">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bb8f0-347">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-347">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="bb8f0-348">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-348">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bb8f0-349">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-349">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="bb8f0-350">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-350">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bb8f0-351">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-351">Generated clients</span></span>

<span data-ttu-id="bb8f0-352">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-352">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bb8f0-353">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-353">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bb8f0-354">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-354">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bb8f0-355">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-355">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bb8f0-356">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-356">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bb8f0-357">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-357">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bb8f0-358">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-358">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bb8f0-359">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="bb8f0-359">Outgoing request middleware</span></span>

<span data-ttu-id="bb8f0-360">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-360">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bb8f0-361">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-361">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="bb8f0-362">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-362">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bb8f0-363">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-363">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bb8f0-364">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-364">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="bb8f0-365">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-365">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="bb8f0-366">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-366">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="bb8f0-367">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-367">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bb8f0-368">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-368">The preceding code defines a basic handler.</span></span> <span data-ttu-id="bb8f0-369">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-369">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="bb8f0-370">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-370">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="bb8f0-371">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-371">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="bb8f0-372">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-372">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="bb8f0-373">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-373">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bb8f0-374">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-374">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="bb8f0-375">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-375">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="bb8f0-376">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-376">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="bb8f0-377">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-377">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="bb8f0-378">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-378">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bb8f0-379">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-379">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bb8f0-380">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-380">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bb8f0-381">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-381">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="bb8f0-382">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-382">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="bb8f0-383">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-383">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bb8f0-384">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-384">Use Polly-based handlers</span></span>

<span data-ttu-id="bb8f0-385">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-385">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bb8f0-386">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-386">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bb8f0-387">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-387">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bb8f0-388">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-388">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-389">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-389">The Polly extensions:</span></span>

* <span data-ttu-id="bb8f0-390">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-390">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="bb8f0-391">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-391">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="bb8f0-392">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-392">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bb8f0-393">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="bb8f0-393">Handle transient faults</span></span>

<span data-ttu-id="bb8f0-394">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-394">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bb8f0-395">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-395">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bb8f0-396">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-396">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="bb8f0-397">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-397">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb8f0-398">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-398">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="bb8f0-399">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-399">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bb8f0-400">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-400">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bb8f0-401">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="bb8f0-401">Dynamically select policies</span></span>

<span data-ttu-id="bb8f0-402">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-402">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="bb8f0-403">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-403">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="bb8f0-404">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-404">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bb8f0-405">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-405">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bb8f0-406">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-406">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bb8f0-407">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-407">Add multiple Polly handlers</span></span>

<span data-ttu-id="bb8f0-408">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-408">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bb8f0-409">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-409">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="bb8f0-410">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-410">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="bb8f0-411">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-411">Failed requests are retried up to three times.</span></span> <span data-ttu-id="bb8f0-412">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-412">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="bb8f0-413">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-413">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="bb8f0-414">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-414">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bb8f0-415">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-415">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bb8f0-416">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-416">Add policies from the Polly registry</span></span>

<span data-ttu-id="bb8f0-417">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-417">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="bb8f0-418">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-418">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="bb8f0-419">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-419">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="bb8f0-420">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-420">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="bb8f0-421">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-421">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bb8f0-422">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="bb8f0-422">HttpClient and lifetime management</span></span>

<span data-ttu-id="bb8f0-423">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-423">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-424">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-424">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="bb8f0-425">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-425">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bb8f0-426">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-426">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bb8f0-427">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-427">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bb8f0-428">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-428">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bb8f0-429">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-429">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bb8f0-430">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-430">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="bb8f0-431">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-431">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bb8f0-432">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-432">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="bb8f0-433">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-433">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="bb8f0-434">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-434">Disposal of the client isn't required.</span></span> <span data-ttu-id="bb8f0-435">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-435">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bb8f0-436">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-436">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-437">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-437">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="bb8f0-438">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-438">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-439">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-439">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="bb8f0-440">protokolování</span><span class="sxs-lookup"><span data-stu-id="bb8f0-440">Logging</span></span>

<span data-ttu-id="bb8f0-441">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-441">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bb8f0-442">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-442">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="bb8f0-443">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-443">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bb8f0-444">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-444">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bb8f0-445">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-445">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="bb8f0-446">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-446">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-447">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-447">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bb8f0-448">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-448">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bb8f0-449">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-449">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-450">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-450">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="bb8f0-451">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-451">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="bb8f0-452">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-452">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bb8f0-453">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-453">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bb8f0-454">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-454">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="bb8f0-455">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-455">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bb8f0-456">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bb8f0-456">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bb8f0-457">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-457">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bb8f0-458">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-458">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bb8f0-459">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-459">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bb8f0-460">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-460">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bb8f0-461">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="bb8f0-461">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bb8f0-462">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-462">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bb8f0-463">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="bb8f0-463">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bb8f0-464">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="bb8f0-464">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bb8f0-465">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-465">In the following example:</span></span>

* <span data-ttu-id="bb8f0-466"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-466"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bb8f0-467">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-467">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bb8f0-468">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-468">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bb8f0-469">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-469">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="bb8f0-470">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bb8f0-470">Additional resources</span></span>

* [<span data-ttu-id="bb8f0-471">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="bb8f0-471">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bb8f0-472">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-472">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bb8f0-473">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="bb8f0-473">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="bb8f0-474">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="bb8f0-474">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="bb8f0-475"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-475">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="bb8f0-476">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-476">It offers the following benefits:</span></span>

* <span data-ttu-id="bb8f0-477">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-477">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-478">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-478">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="bb8f0-479">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-479">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="bb8f0-480">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-480">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="bb8f0-481">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-481">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="bb8f0-482">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-482">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="bb8f0-483">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb8f0-483">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bb8f0-484">Požadavky</span><span class="sxs-lookup"><span data-stu-id="bb8f0-484">Prerequisites</span></span>

<span data-ttu-id="bb8f0-485">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-485">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="bb8f0-486">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už obsahují balíček `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-486">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="bb8f0-487">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="bb8f0-487">Consumption patterns</span></span>

<span data-ttu-id="bb8f0-488">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-488">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="bb8f0-489">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-489">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="bb8f0-490">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-490">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="bb8f0-491">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-491">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="bb8f0-492">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-492">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="bb8f0-493">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-493">None of them are strictly superior to another.</span></span> <span data-ttu-id="bb8f0-494">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-494">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="bb8f0-495">Základní využití</span><span class="sxs-lookup"><span data-stu-id="bb8f0-495">Basic usage</span></span>

<span data-ttu-id="bb8f0-496">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-496">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="bb8f0-497">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-497">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bb8f0-498">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-498">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="bb8f0-499">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-499">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="bb8f0-500">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-500">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="bb8f0-501">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-501">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="bb8f0-502">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-502">Named clients</span></span>

<span data-ttu-id="bb8f0-503">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-503">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="bb8f0-504">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-504">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="bb8f0-505">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-505">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="bb8f0-506">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-506">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="bb8f0-507">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-507">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="bb8f0-508">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-508">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="bb8f0-509">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-509">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="bb8f0-510">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-510">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="bb8f0-511">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-511">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="bb8f0-512">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="bb8f0-512">Typed clients</span></span>

<span data-ttu-id="bb8f0-513">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-513">Typed clients:</span></span>

* <span data-ttu-id="bb8f0-514">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-514">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="bb8f0-515">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-515">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="bb8f0-516">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-516">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="bb8f0-517">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-517">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="bb8f0-518">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-518">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="bb8f0-519">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-519">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bb8f0-520">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-520">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="bb8f0-521">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-521">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="bb8f0-522">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-522">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="bb8f0-523">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-523">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="bb8f0-524">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-524">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="bb8f0-525">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-525">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="bb8f0-526">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-526">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="bb8f0-527">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-527">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="bb8f0-528">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-528">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="bb8f0-529">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-529">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="bb8f0-530">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-530">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="bb8f0-531">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-531">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="bb8f0-532">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="bb8f0-532">Generated clients</span></span>

<span data-ttu-id="bb8f0-533">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-533">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="bb8f0-534">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-534">Refit is a REST library for .NET.</span></span> <span data-ttu-id="bb8f0-535">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-535">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="bb8f0-536">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-536">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="bb8f0-537">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-537">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="bb8f0-538">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-538">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="bb8f0-539">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-539">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="bb8f0-540">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="bb8f0-540">Outgoing request middleware</span></span>

<span data-ttu-id="bb8f0-541">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-541">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="bb8f0-542">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-542">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="bb8f0-543">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-543">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="bb8f0-544">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-544">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="bb8f0-545">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-545">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="bb8f0-546">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-546">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="bb8f0-547">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-547">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="bb8f0-548">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-548">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="bb8f0-549">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-549">The preceding code defines a basic handler.</span></span> <span data-ttu-id="bb8f0-550">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-550">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="bb8f0-551">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-551">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="bb8f0-552">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-552">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="bb8f0-553">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-553">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="bb8f0-554">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-554">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="bb8f0-555">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-555">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="bb8f0-556">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-556">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="bb8f0-557">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-557">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="bb8f0-558">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-558">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="bb8f0-559">Po zaregistrování můžete <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> volat a předat do něj typ obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-559">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="bb8f0-560">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-560">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="bb8f0-561">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-561">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="bb8f0-562">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-562">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="bb8f0-563">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-563">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="bb8f0-564">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-564">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="bb8f0-565">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-565">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="bb8f0-566">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-566">Use Polly-based handlers</span></span>

<span data-ttu-id="bb8f0-567">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-567">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="bb8f0-568">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-568">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="bb8f0-569">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-569">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="bb8f0-570">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-570">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-571">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-571">The Polly extensions:</span></span>

* <span data-ttu-id="bb8f0-572">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-572">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="bb8f0-573">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-573">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="bb8f0-574">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-574">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="bb8f0-575">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="bb8f0-575">Handle transient faults</span></span>

<span data-ttu-id="bb8f0-576">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-576">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="bb8f0-577">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-577">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="bb8f0-578">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-578">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="bb8f0-579">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-579">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb8f0-580">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-580">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="bb8f0-581">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-581">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="bb8f0-582">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-582">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="bb8f0-583">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="bb8f0-583">Dynamically select policies</span></span>

<span data-ttu-id="bb8f0-584">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-584">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="bb8f0-585">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-585">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="bb8f0-586">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-586">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="bb8f0-587">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-587">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="bb8f0-588">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-588">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="bb8f0-589">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-589">Add multiple Polly handlers</span></span>

<span data-ttu-id="bb8f0-590">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-590">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="bb8f0-591">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-591">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="bb8f0-592">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-592">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="bb8f0-593">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-593">Failed requests are retried up to three times.</span></span> <span data-ttu-id="bb8f0-594">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-594">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="bb8f0-595">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-595">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="bb8f0-596">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-596">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="bb8f0-597">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-597">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="bb8f0-598">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-598">Add policies from the Polly registry</span></span>

<span data-ttu-id="bb8f0-599">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-599">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="bb8f0-600">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-600">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="bb8f0-601">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-601">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="bb8f0-602">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-602">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="bb8f0-603">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="bb8f0-603">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="bb8f0-604">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="bb8f0-604">HttpClient and lifetime management</span></span>

<span data-ttu-id="bb8f0-605">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-605">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-606">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-606">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="bb8f0-607">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-607">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="bb8f0-608">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-608">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="bb8f0-609">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-609">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="bb8f0-610">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-610">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="bb8f0-611">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-611">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="bb8f0-612">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-612">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="bb8f0-613">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-613">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="bb8f0-614">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-614">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="bb8f0-615">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-615">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="bb8f0-616">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-616">Disposal of the client isn't required.</span></span> <span data-ttu-id="bb8f0-617">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-617">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="bb8f0-618">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-618">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="bb8f0-619">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-619">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="bb8f0-620">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-620">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="bb8f0-621">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-621">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="bb8f0-622">protokolování</span><span class="sxs-lookup"><span data-stu-id="bb8f0-622">Logging</span></span>

<span data-ttu-id="bb8f0-623">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-623">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="bb8f0-624">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-624">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="bb8f0-625">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-625">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="bb8f0-626">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-626">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="bb8f0-627">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-627">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="bb8f0-628">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-628">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-629">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-629">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="bb8f0-630">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-630">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="bb8f0-631">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-631">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="bb8f0-632">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-632">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="bb8f0-633">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-633">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="bb8f0-634">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-634">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="bb8f0-635">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-635">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="bb8f0-636">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-636">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="bb8f0-637">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-637">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="bb8f0-638">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="bb8f0-638">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="bb8f0-639">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-639">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="bb8f0-640">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-640">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="bb8f0-641">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-641">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="bb8f0-642">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-642">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="bb8f0-643">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="bb8f0-643">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="bb8f0-644">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-644">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="bb8f0-645">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="bb8f0-645">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="bb8f0-646">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="bb8f0-646">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="bb8f0-647">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bb8f0-647">In the following example:</span></span>

* <span data-ttu-id="bb8f0-648"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="bb8f0-648"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="bb8f0-649">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-649">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="bb8f0-650">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-650">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="bb8f0-651">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="bb8f0-651">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="bb8f0-652">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bb8f0-652">Additional resources</span></span>

* [<span data-ttu-id="bb8f0-653">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="bb8f0-653">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="bb8f0-654">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="bb8f0-654">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="bb8f0-655">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="bb8f0-655">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
