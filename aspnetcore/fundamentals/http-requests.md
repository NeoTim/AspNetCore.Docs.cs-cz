---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ef6ed0911c2615ea543b1c2cea31a9a453f0c604
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017230"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="c4231-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4231-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c4231-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin</span><span class="sxs-lookup"><span data-stu-id="c4231-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="c4231-105">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c4231-106">`IHttpClientFactory`nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="c4231-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="c4231-107">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c4231-108">Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c4231-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c4231-109">Pro obecný přístup je možné zaregistrovat výchozího klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="c4231-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="c4231-111">Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="c4231-112">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="c4231-113">Automatická správa zabraňuje běžným problémům se službou DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="c4231-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c4231-114">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="c4231-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c4231-115">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="c4231-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c4231-116">Vzorový kód v tomto tématu verze používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích http.</span><span class="sxs-lookup"><span data-stu-id="c4231-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="c4231-117">V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>` , použijte selektor verzí k výběru verze 2. x tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="c4231-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c4231-118">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="c4231-118">Consumption patterns</span></span>

<span data-ttu-id="c4231-119">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="c4231-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c4231-120">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c4231-121">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c4231-122">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c4231-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c4231-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c4231-125">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-125">Basic usage</span></span>

<span data-ttu-id="c4231-126">`IHttpClientFactory`lze zaregistrovat voláním `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="c4231-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c4231-127">`IHttpClientFactory`Může být požadováno pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c4231-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c4231-128">Následující kód používá `IHttpClientFactory` k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="c4231-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c4231-129">Použití `IHttpClientFactory` Like v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="c4231-130">Nemá žádný vliv na to, jak `HttpClient` se používá.</span><span class="sxs-lookup"><span data-stu-id="c4231-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="c4231-131">Na místech `HttpClient` , kde jsou vytvořeny instance v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c4231-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-132">Named clients</span></span>

<span data-ttu-id="c4231-133">Pojmenovaná klienti jsou dobrou volbou v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="c4231-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="c4231-134">Aplikace vyžaduje mnoho různých použití `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="c4231-135">Mnoho `HttpClient` s má odlišnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="c4231-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="c4231-136">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c4231-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c4231-137">V předchozím kódu je klient nakonfigurován pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="c4231-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="c4231-138">Základní adresa `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="c4231-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="c4231-139">Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.</span><span class="sxs-lookup"><span data-stu-id="c4231-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="c4231-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="c4231-140">CreateClient</span></span>

<span data-ttu-id="c4231-141">Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*> se zavolá:</span><span class="sxs-lookup"><span data-stu-id="c4231-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="c4231-142">Vytvoří se nová instance `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="c4231-143">Je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c4231-143">The configuration action is called.</span></span>

<span data-ttu-id="c4231-144">Chcete-li vytvořit pojmenovaného klienta, předejte jeho jméno do `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="c4231-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c4231-145">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="c4231-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c4231-146">Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c4231-147">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-147">Typed clients</span></span>

<span data-ttu-id="c4231-148">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="c4231-148">Typed clients:</span></span>

* <span data-ttu-id="c4231-149">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="c4231-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c4231-150">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c4231-151">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c4231-152">Můžete například použít jednoho typu klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="c4231-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="c4231-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="c4231-154">Zapouzdřit veškerou práci s koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="c4231-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="c4231-155">Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="c4231-156">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c4231-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c4231-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="c4231-157">In the preceding code:</span></span>

* <span data-ttu-id="c4231-158">Konfigurace je přesunuta do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="c4231-159">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c4231-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="c4231-160">Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkce.</span><span class="sxs-lookup"><span data-stu-id="c4231-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c4231-161">Například `GetAspNetDocsIssues` Metoda zapouzdřuje kód pro načtení otevřených problémů.</span><span class="sxs-lookup"><span data-stu-id="c4231-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="c4231-162">Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c4231-163">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="c4231-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c4231-164">V předchozím kódu se `AddHttpClient` registruje `GitHubService` jako dočasná služba.</span><span class="sxs-lookup"><span data-stu-id="c4231-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="c4231-165">Tato registrace používá metodu továrny k těmto účelům:</span><span class="sxs-lookup"><span data-stu-id="c4231-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="c4231-166">Vytvořte instanci `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c4231-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="c4231-167">Vytvořte instanci objektu s `GitHubService` předáním v instanci `HttpClient` do jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c4231-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="c4231-168">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="c4231-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c4231-169">Konfiguraci pro typového klienta lze zadat během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c4231-170">`HttpClient`Může být zapouzdřen v rámci typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="c4231-171">Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která volá `HttpClient` interně instanci:</span><span class="sxs-lookup"><span data-stu-id="c4231-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c4231-172">V předchozím kódu `HttpClient` je uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="c4231-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="c4231-173">Přístup k aplikaci `HttpClient` je veřejný `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="c4231-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c4231-174">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-174">Generated clients</span></span>

<span data-ttu-id="c4231-175">`IHttpClientFactory`dá se použít v kombinaci s knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c4231-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c4231-176">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c4231-177">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4231-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c4231-178">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="c4231-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c4231-179">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="c4231-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="c4231-180">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="c4231-181">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="c4231-182">Vytváření požadavků POST, PUT a DELETE</span><span class="sxs-lookup"><span data-stu-id="c4231-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="c4231-183">V předchozích příkladech všechny požadavky HTTP používají příkaz GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="c4231-184">`HttpClient`podporuje také jiné příkazy HTTP, včetně:</span><span class="sxs-lookup"><span data-stu-id="c4231-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="c4231-185">POST</span><span class="sxs-lookup"><span data-stu-id="c4231-185">POST</span></span>
* <span data-ttu-id="c4231-186">PUT</span><span class="sxs-lookup"><span data-stu-id="c4231-186">PUT</span></span>
* <span data-ttu-id="c4231-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="c4231-187">DELETE</span></span>
* <span data-ttu-id="c4231-188">POUŽITA</span><span class="sxs-lookup"><span data-stu-id="c4231-188">PATCH</span></span>

<span data-ttu-id="c4231-189">Úplný seznam podporovaných příkazů HTTP naleznete v tématu <xref:System.Net.Http.HttpMethod> .</span><span class="sxs-lookup"><span data-stu-id="c4231-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="c4231-190">Následující příklad ukazuje, jak vytvořit požadavek HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="c4231-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="c4231-191">V předchozím kódu `CreateItemAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="c4231-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="c4231-192">Zaserializace `TodoItem` parametr do formátu JSON pomocí `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="c4231-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="c4231-193">Tato operace používá instanci <xref:System.Text.Json.JsonSerializerOptions> ke konfiguraci procesu serializace.</span><span class="sxs-lookup"><span data-stu-id="c4231-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="c4231-194">Vytvoří instanci <xref:System.Net.Http.StringContent> pro zabalení serializovaného JSON pro odeslání v těle požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="c4231-195">Volání <xref:System.Net.Http.HttpClient.PostAsync%2A> pro odeslání obsahu JSON na zadanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c4231-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="c4231-196">Toto je relativní adresa URL, která se přidá do [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span><span class="sxs-lookup"><span data-stu-id="c4231-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="c4231-197">Volání <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> vyvolávající výjimku, pokud kód stavu odpovědi neindikuje úspěch.</span><span class="sxs-lookup"><span data-stu-id="c4231-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="c4231-198">`HttpClient`podporuje také další typy obsahu.</span><span class="sxs-lookup"><span data-stu-id="c4231-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="c4231-199">Příklad: <xref:System.Net.Http.MultipartContent> a <xref:System.Net.Http.StreamContent>.</span><span class="sxs-lookup"><span data-stu-id="c4231-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="c4231-200">Úplný seznam podporovaného obsahu najdete v tématu <xref:System.Net.Http.HttpContent> .</span><span class="sxs-lookup"><span data-stu-id="c4231-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="c4231-201">Následující příklad ukazuje požadavek HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="c4231-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="c4231-202">Předchozí kód je velmi podobný jako příklad příspěvku.</span><span class="sxs-lookup"><span data-stu-id="c4231-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="c4231-203">`SaveItemAsync`Metoda volá <xref:System.Net.Http.HttpClient.PutAsync%2A> místo `PostAsync` .</span><span class="sxs-lookup"><span data-stu-id="c4231-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="c4231-204">Následující příklad ukazuje požadavek HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="c4231-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="c4231-205">V předchozím kódu `DeleteItemAsync` metoda volá metodu <xref:System.Net.Http.HttpClient.DeleteAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="c4231-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="c4231-206">Vzhledem k tomu, že žádosti o odstranění HTTP obvykle neobsahují žádné tělo, `DeleteAsync` Metoda neposkytne přetížení, které přijímá instanci `HttpContent` .</span><span class="sxs-lookup"><span data-stu-id="c4231-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="c4231-207">Další informace o používání různých příkazů HTTP s najdete `HttpClient` v tématu <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="c4231-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c4231-208">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="c4231-208">Outgoing request middleware</span></span>

<span data-ttu-id="c4231-209">`HttpClient`má koncepci delegování obslužných rutin, které mohou být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c4231-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="c4231-210">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="c4231-211">Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-211">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="c4231-212">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4231-212">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c4231-213">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="c4231-213">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c4231-214">Tento model:</span><span class="sxs-lookup"><span data-stu-id="c4231-214">This pattern:</span></span>

  * <span data-ttu-id="c4231-215">Se podobá příchozímu kanálu middleware v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4231-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="c4231-216">Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:</span><span class="sxs-lookup"><span data-stu-id="c4231-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="c4231-217">vyrovnávací</span><span class="sxs-lookup"><span data-stu-id="c4231-217">caching</span></span>
    * <span data-ttu-id="c4231-218">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="c4231-218">error handling</span></span>
    * <span data-ttu-id="c4231-219">serializace</span><span class="sxs-lookup"><span data-stu-id="c4231-219">serialization</span></span>
    * <span data-ttu-id="c4231-220">protokolování</span><span class="sxs-lookup"><span data-stu-id="c4231-220">logging</span></span>

<span data-ttu-id="c4231-221">Vytvoření obslužné rutiny delegování:</span><span class="sxs-lookup"><span data-stu-id="c4231-221">To create a delegating handler:</span></span>

* <span data-ttu-id="c4231-222">Odvodit z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="c4231-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="c4231-223">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="c4231-224">Před předáním požadavku další obslužné rutině v kanálu spusťte kód:</span><span class="sxs-lookup"><span data-stu-id="c4231-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c4231-225">Předchozí kód zkontroluje, zda `X-API-KEY` je hlavička v žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="c4231-226">Pokud `X-API-KEY` chybí, <xref:System.Net.HttpStatusCode.BadRequest> je vrácena.</span><span class="sxs-lookup"><span data-stu-id="c4231-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="c4231-227">Do konfigurace pro se dá přidat víc než jedna obslužná rutina `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="c4231-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c4231-228">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="c4231-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c4231-229">`IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-229">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c4231-230">Obslužné rutiny mohou záviset na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="c4231-230">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="c4231-231">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-231">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c4231-232">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-232">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c4231-233">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c4231-233">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c4231-234">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="c4231-234">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c4231-235">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c4231-235">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c4231-236">Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="c4231-236">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="c4231-237"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="c4231-237">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="c4231-238">Vytvořte vlastní <xref:System.Threading.AsyncLocal`1> objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="c4231-238">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c4231-239">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-239">Use Polly-based handlers</span></span>

<span data-ttu-id="c4231-240">`IHttpClientFactory`integruje se s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany.</span><span class="sxs-lookup"><span data-stu-id="c4231-240">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c4231-241">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-241">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c4231-242">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c4231-242">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c4231-243">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-243">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c4231-244">Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-244">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="c4231-245">Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="c4231-245">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c4231-246">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="c4231-246">Handle transient faults</span></span>

<span data-ttu-id="c4231-247">K chybám obvykle dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="c4231-247">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c4231-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="c4231-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c4231-249">Zásady nakonfigurované s nástrojem `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="c4231-249">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="c4231-250">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="c4231-250">HTTP 5xx</span></span>
* <span data-ttu-id="c4231-251">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="c4231-251">HTTP 408</span></span>

<span data-ttu-id="c4231-252">`AddTransientHttpErrorPolicy`poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="c4231-252">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="c4231-253">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="c4231-253">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c4231-254">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="c4231-254">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c4231-255">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="c4231-255">Dynamically select policies</span></span>

<span data-ttu-id="c4231-256">Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-256">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="c4231-257">Následující `AddPolicyHandler` přetížení zkontroluje požadavek na rozhodnutí, které zásady se mají použít:</span><span class="sxs-lookup"><span data-stu-id="c4231-257">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c4231-258">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-258">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c4231-259">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-259">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c4231-260">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-260">Add multiple Polly handlers</span></span>

<span data-ttu-id="c4231-261">Je běžné vnořovat zásady Polly:</span><span class="sxs-lookup"><span data-stu-id="c4231-261">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c4231-262">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c4231-262">In the preceding example:</span></span>

* <span data-ttu-id="c4231-263">Přidávají se dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-263">Two handlers are added.</span></span>
* <span data-ttu-id="c4231-264">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="c4231-264">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="c4231-265">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="c4231-265">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="c4231-266">Druhé `AddTransientHttpErrorPolicy` volání přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-266">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="c4231-267">Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="c4231-267">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="c4231-268">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="c4231-268">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c4231-269">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-269">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c4231-270">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-270">Add policies from the Polly registry</span></span>

<span data-ttu-id="c4231-271">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="c4231-271">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="c4231-272">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="c4231-272">In the following code:</span></span>

* <span data-ttu-id="c4231-273">Přidají se zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="c4231-273">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="c4231-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>Přidá do registru zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="c4231-274"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="c4231-275">Další informace o `IHttpClientFactory` Polly integrech a integraci najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c4231-275">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c4231-276">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c4231-276">HttpClient and lifetime management</span></span>

<span data-ttu-id="c4231-277">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-277">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-278"><xref:System.Net.Http.HttpMessageHandler>Vytvoří se pro jednotlivé pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="c4231-278">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="c4231-279">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-279">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c4231-280">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="c4231-280">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c4231-281">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="c4231-281">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c4231-282">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-282">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c4231-283">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="c4231-283">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c4231-284">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="c4231-284">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="c4231-285">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="c4231-285">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c4231-286">Výchozí hodnota může být přepsána podle pojmenovaných klientů:</span><span class="sxs-lookup"><span data-stu-id="c4231-286">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="c4231-287">`HttpClient`instance je obecně možné považovat **za objekty .NET, které** nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="c4231-287">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="c4231-288">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-288">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c4231-289">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-289">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="c4231-290">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-290">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-291">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-291">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c4231-292">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c4231-292">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c4231-293">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="c4231-293">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c4231-294">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-294">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c4231-295">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="c4231-295">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c4231-296">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="c4231-296">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c4231-297">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-297">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c4231-298">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-298">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c4231-299">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="c4231-299">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c4231-300">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="c4231-300">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c4231-301">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-301">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c4231-302">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="c4231-302">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c4231-303">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-303">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c4231-304">Cookiepracují</span><span class="sxs-lookup"><span data-stu-id="c4231-304">Cookies</span></span>

<span data-ttu-id="c4231-305">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="c4231-305">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c4231-306">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="c4231-306">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c4231-307">U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="c4231-307">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c4231-308">Zákaz automatického cookie zpracování</span><span class="sxs-lookup"><span data-stu-id="c4231-308">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c4231-309">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c4231-309">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c4231-310">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:</span><span class="sxs-lookup"><span data-stu-id="c4231-310">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c4231-311">protokolování</span><span class="sxs-lookup"><span data-stu-id="c4231-311">Logging</span></span>

<span data-ttu-id="c4231-312">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c4231-312">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c4231-313">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="c4231-313">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="c4231-314">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="c4231-314">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c4231-315">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-315">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c4231-316">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="c4231-316">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="c4231-317">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-317">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c4231-318">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-318">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c4231-319">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="c4231-319">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c4231-320">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-320">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c4231-321">V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="c4231-321">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="c4231-322">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-322">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="c4231-323">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-323">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c4231-324">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-324">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c4231-325">To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c4231-325">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="c4231-326">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="c4231-326">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c4231-327">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c4231-327">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c4231-328">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-328">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c4231-329">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="c4231-329">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c4231-330"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="c4231-330">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c4231-331">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-331">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c4231-332">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="c4231-332">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c4231-333">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="c4231-333">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c4231-334">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="c4231-334">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c4231-335">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="c4231-335">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c4231-336">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c4231-336">In the following example:</span></span>

* <span data-ttu-id="c4231-337"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="c4231-337"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c4231-338">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-338">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c4231-339">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="c4231-339">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c4231-340">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c4231-340">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="c4231-341">Middleware šíření hlaviček</span><span class="sxs-lookup"><span data-stu-id="c4231-341">Header propagation middleware</span></span>

<span data-ttu-id="c4231-342">Šíření hlaviček je ASP.NET Core middleware pro šíření hlaviček protokolu HTTP z příchozího požadavku do odchozích požadavků klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-342">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="c4231-343">Použití rozšíření hlavičky:</span><span class="sxs-lookup"><span data-stu-id="c4231-343">To use header propagation:</span></span>

* <span data-ttu-id="c4231-344">Odkázat na balíček [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="c4231-344">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="c4231-345">Konfigurace middlewaru a `HttpClient` v `Startup` :</span><span class="sxs-lookup"><span data-stu-id="c4231-345">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="c4231-346">Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:</span><span class="sxs-lookup"><span data-stu-id="c4231-346">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="c4231-347">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c4231-347">Additional resources</span></span>

* [<span data-ttu-id="c4231-348">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="c4231-348">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c4231-349">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-349">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c4231-350">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="c4231-350">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="c4231-351">Postup serializace a deserializace JSON v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="c4231-351">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c4231-352">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c4231-352">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c4231-353">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-353">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c4231-354">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="c4231-354">It offers the following benefits:</span></span>

* <span data-ttu-id="c4231-355">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-355">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c4231-356">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c4231-356">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c4231-357">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="c4231-357">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c4231-358">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="c4231-358">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c4231-359">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="c4231-359">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c4231-360">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="c4231-360">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c4231-361">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c4231-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c4231-362">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="c4231-362">Consumption patterns</span></span>

<span data-ttu-id="c4231-363">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="c4231-363">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c4231-364">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-364">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c4231-365">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-365">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c4231-366">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-366">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c4231-367">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-367">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c4231-368">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="c4231-368">None of them are strictly superior to another.</span></span> <span data-ttu-id="c4231-369">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-369">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c4231-370">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-370">Basic usage</span></span>

<span data-ttu-id="c4231-371">`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="c4231-371">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c4231-372">Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c4231-372">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c4231-373">`IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="c4231-373">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c4231-374">`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-374">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c4231-375">Nemá žádný vliv na způsob `HttpClient` použití.</span><span class="sxs-lookup"><span data-stu-id="c4231-375">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c4231-376">Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-376">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c4231-377">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-377">Named clients</span></span>

<span data-ttu-id="c4231-378">Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="c4231-378">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c4231-379">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c4231-379">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c4231-380">V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="c4231-380">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c4231-381">U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="c4231-381">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c4231-382">Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c4231-382">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c4231-383">Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-383">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c4231-384">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="c4231-384">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c4231-385">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="c4231-385">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c4231-386">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-386">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c4231-387">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-387">Typed clients</span></span>

<span data-ttu-id="c4231-388">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="c4231-388">Typed clients:</span></span>

* <span data-ttu-id="c4231-389">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="c4231-389">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c4231-390">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-390">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c4231-391">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-391">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c4231-392">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="c4231-392">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c4231-393">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-393">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c4231-394">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c4231-394">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c4231-395">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-395">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c4231-396">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c4231-396">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c4231-397">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost.</span><span class="sxs-lookup"><span data-stu-id="c4231-397">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c4231-398">`GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="c4231-398">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c4231-399">K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-399">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c4231-400">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="c4231-400">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c4231-401">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="c4231-401">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c4231-402">Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-402">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c4231-403">Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-403">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c4231-404">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.</span><span class="sxs-lookup"><span data-stu-id="c4231-404">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c4231-405">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="c4231-405">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c4231-406">Veškerý přístup k externím voláním prochází `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="c4231-406">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c4231-407">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-407">Generated clients</span></span>

<span data-ttu-id="c4231-408">`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c4231-408">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c4231-409">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-409">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c4231-410">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4231-410">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c4231-411">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="c4231-411">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c4231-412">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="c4231-412">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="c4231-413">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-413">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="c4231-414">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-414">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c4231-415">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="c4231-415">Outgoing request middleware</span></span>

<span data-ttu-id="c4231-416">`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-416">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c4231-417">`IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-417">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c4231-418">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4231-418">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c4231-419">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="c4231-419">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c4231-420">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4231-420">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c4231-421">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="c4231-421">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c4231-422">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="c4231-422">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c4231-423">`SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="c4231-423">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c4231-424">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-424">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c4231-425">Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička.</span><span class="sxs-lookup"><span data-stu-id="c4231-425">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c4231-426">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="c4231-426">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c4231-427">Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-427">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c4231-428">Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c4231-428">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c4231-429">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="c4231-429">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c4231-430">`IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-430">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="c4231-431">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="c4231-431">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="c4231-432">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-432">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="c4231-433">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-433">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="c4231-434">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c4231-434">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c4231-435">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="c4231-435">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c4231-436">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c4231-436">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c4231-437">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="c4231-437">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c4231-438">`IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="c4231-438">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c4231-439">Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="c4231-439">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c4231-440">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-440">Use Polly-based handlers</span></span>

<span data-ttu-id="c4231-441">`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="c4231-441">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c4231-442">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-442">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c4231-443">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c4231-443">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c4231-444">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-444">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c4231-445">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="c4231-445">The Polly extensions:</span></span>

* <span data-ttu-id="c4231-446">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-446">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c4231-447">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="c4231-447">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c4231-448">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4231-448">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c4231-449">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="c4231-449">Handle transient faults</span></span>

<span data-ttu-id="c4231-450">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="c4231-450">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c4231-451">`AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="c4231-451">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c4231-452">Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.</span><span class="sxs-lookup"><span data-stu-id="c4231-452">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c4231-453">`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c4231-453">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4231-454">Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="c4231-454">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c4231-455">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="c4231-455">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c4231-456">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="c4231-456">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c4231-457">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="c4231-457">Dynamically select policies</span></span>

<span data-ttu-id="c4231-458">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="c4231-458">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c4231-459">Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="c4231-459">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c4231-460">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="c4231-460">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c4231-461">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-461">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c4231-462">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-462">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c4231-463">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-463">Add multiple Polly handlers</span></span>

<span data-ttu-id="c4231-464">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="c4231-464">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c4231-465">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-465">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c4231-466">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="c4231-466">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c4231-467">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="c4231-467">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c4231-468">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-468">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c4231-469">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="c4231-469">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c4231-470">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="c4231-470">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c4231-471">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-471">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c4231-472">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-472">Add policies from the Polly registry</span></span>

<span data-ttu-id="c4231-473">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="c4231-473">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c4231-474">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="c4231-474">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c4231-475">V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="c4231-475">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c4231-476">Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="c4231-476">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c4231-477">Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c4231-477">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c4231-478">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c4231-478">HttpClient and lifetime management</span></span>

<span data-ttu-id="c4231-479">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-479">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-480">Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-480">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c4231-481">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-481">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c4231-482">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="c4231-482">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c4231-483">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="c4231-483">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c4231-484">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-484">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c4231-485">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="c4231-485">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c4231-486">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-486">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c4231-487">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="c4231-487">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c4231-488">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-488">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c4231-489">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-489">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c4231-490">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="c4231-490">Disposal of the client isn't required.</span></span> <span data-ttu-id="c4231-491">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-491">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c4231-492">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-492">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c4231-493">`HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="c4231-493">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c4231-494">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-494">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-495">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-495">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c4231-496">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c4231-496">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c4231-497">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="c4231-497">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c4231-498">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-498">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c4231-499">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="c4231-499">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c4231-500">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="c4231-500">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c4231-501">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-501">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c4231-502">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-502">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c4231-503">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="c4231-503">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c4231-504">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="c4231-504">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c4231-505">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-505">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c4231-506">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="c4231-506">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c4231-507">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-507">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c4231-508">Cookiepracují</span><span class="sxs-lookup"><span data-stu-id="c4231-508">Cookies</span></span>

<span data-ttu-id="c4231-509">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="c4231-509">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c4231-510">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="c4231-510">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c4231-511">U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="c4231-511">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c4231-512">Zákaz automatického cookie zpracování</span><span class="sxs-lookup"><span data-stu-id="c4231-512">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c4231-513">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c4231-513">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c4231-514">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:</span><span class="sxs-lookup"><span data-stu-id="c4231-514">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c4231-515">protokolování</span><span class="sxs-lookup"><span data-stu-id="c4231-515">Logging</span></span>

<span data-ttu-id="c4231-516">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c4231-516">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c4231-517">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="c4231-517">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c4231-518">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="c4231-518">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c4231-519">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-519">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c4231-520">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="c4231-520">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c4231-521">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-521">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c4231-522">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-522">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c4231-523">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="c4231-523">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c4231-524">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-524">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c4231-525">V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="c4231-525">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c4231-526">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="c4231-526">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c4231-527">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-527">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c4231-528">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-528">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c4231-529">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c4231-529">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c4231-530">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="c4231-530">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c4231-531">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c4231-531">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c4231-532">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-532">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c4231-533">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="c4231-533">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c4231-534"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="c4231-534">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c4231-535">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-535">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c4231-536">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="c4231-536">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c4231-537">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="c4231-537">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c4231-538">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="c4231-538">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c4231-539">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="c4231-539">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c4231-540">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c4231-540">In the following example:</span></span>

* <span data-ttu-id="c4231-541"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="c4231-541"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c4231-542">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-542">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c4231-543">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="c4231-543">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c4231-544">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c4231-544">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="c4231-545">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c4231-545">Additional resources</span></span>

* [<span data-ttu-id="c4231-546">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="c4231-546">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c4231-547">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-547">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c4231-548">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="c4231-548">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c4231-549">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="c4231-549">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="c4231-550">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-550">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="c4231-551">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="c4231-551">It offers the following benefits:</span></span>

* <span data-ttu-id="c4231-552">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-552">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="c4231-553">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="c4231-553">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="c4231-554">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="c4231-554">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="c4231-555">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="c4231-555">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="c4231-556">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="c4231-556">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="c4231-557">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="c4231-557">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="c4231-558">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c4231-558">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c4231-559">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c4231-559">Prerequisites</span></span>

<span data-ttu-id="c4231-560">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="c4231-560">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="c4231-561">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už `Microsoft.Extensions.Http` balíček obsahuje.</span><span class="sxs-lookup"><span data-stu-id="c4231-561">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="c4231-562">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="c4231-562">Consumption patterns</span></span>

<span data-ttu-id="c4231-563">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="c4231-563">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="c4231-564">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-564">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="c4231-565">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-565">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="c4231-566">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-566">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="c4231-567">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-567">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="c4231-568">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="c4231-568">None of them are strictly superior to another.</span></span> <span data-ttu-id="c4231-569">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-569">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="c4231-570">Základní použití</span><span class="sxs-lookup"><span data-stu-id="c4231-570">Basic usage</span></span>

<span data-ttu-id="c4231-571">`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="c4231-571">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="c4231-572">Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c4231-572">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c4231-573">`IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="c4231-573">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="c4231-574">`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-574">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="c4231-575">Nemá žádný vliv na způsob `HttpClient` použití.</span><span class="sxs-lookup"><span data-stu-id="c4231-575">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="c4231-576">Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-576">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="c4231-577">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-577">Named clients</span></span>

<span data-ttu-id="c4231-578">Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="c4231-578">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="c4231-579">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c4231-579">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="c4231-580">V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="c4231-580">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="c4231-581">U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="c4231-581">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="c4231-582">Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c4231-582">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="c4231-583">Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-583">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="c4231-584">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="c4231-584">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c4231-585">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="c4231-585">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="c4231-586">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-586">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="c4231-587">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="c4231-587">Typed clients</span></span>

<span data-ttu-id="c4231-588">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="c4231-588">Typed clients:</span></span>

* <span data-ttu-id="c4231-589">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="c4231-589">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="c4231-590">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-590">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="c4231-591">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-591">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="c4231-592">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="c4231-592">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="c4231-593">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c4231-593">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="c4231-594">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="c4231-594">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c4231-595">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-595">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="c4231-596">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c4231-596">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="c4231-597">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost.</span><span class="sxs-lookup"><span data-stu-id="c4231-597">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="c4231-598">`GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="c4231-598">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="c4231-599">K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-599">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="c4231-600">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="c4231-600">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="c4231-601">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="c4231-601">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="c4231-602">Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-602">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="c4231-603">Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-603">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="c4231-604">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.</span><span class="sxs-lookup"><span data-stu-id="c4231-604">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="c4231-605">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="c4231-605">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="c4231-606">Veškerý přístup k externím voláním prochází `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="c4231-606">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="c4231-607">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="c4231-607">Generated clients</span></span>

<span data-ttu-id="c4231-608">`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="c4231-608">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="c4231-609">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-609">Refit is a REST library for .NET.</span></span> <span data-ttu-id="c4231-610">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4231-610">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="c4231-611">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="c4231-611">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="c4231-612">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="c4231-612">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="c4231-613">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-613">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="c4231-614">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="c4231-614">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="c4231-615">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="c4231-615">Outgoing request middleware</span></span>

<span data-ttu-id="c4231-616">`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-616">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c4231-617">`IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-617">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="c4231-618">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4231-618">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="c4231-619">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="c4231-619">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="c4231-620">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c4231-620">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="c4231-621">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="c4231-621">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="c4231-622">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="c4231-622">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="c4231-623">`SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="c4231-623">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="c4231-624">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="c4231-624">The preceding code defines a basic handler.</span></span> <span data-ttu-id="c4231-625">Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička.</span><span class="sxs-lookup"><span data-stu-id="c4231-625">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="c4231-626">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="c4231-626">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="c4231-627">Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-627">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="c4231-628">Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c4231-628">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="c4231-629">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="c4231-629">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="c4231-630">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="c4231-630">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="c4231-631">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="c4231-631">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="c4231-632">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="c4231-632">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="c4231-633">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-633">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="c4231-634">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné je volat s předáním do typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-634">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="c4231-635">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="c4231-635">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="c4231-636">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="c4231-636">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="c4231-637">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="c4231-637">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="c4231-638">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="c4231-638">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="c4231-639">`IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="c4231-639">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="c4231-640">Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="c4231-640">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="c4231-641">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-641">Use Polly-based handlers</span></span>

<span data-ttu-id="c4231-642">`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="c4231-642">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="c4231-643">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="c4231-643">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="c4231-644">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="c4231-644">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="c4231-645">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-645">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="c4231-646">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="c4231-646">The Polly extensions:</span></span>

* <span data-ttu-id="c4231-647">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-647">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="c4231-648">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="c4231-648">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="c4231-649">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c4231-649">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="c4231-650">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="c4231-650">Handle transient faults</span></span>

<span data-ttu-id="c4231-651">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="c4231-651">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="c4231-652">`AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="c4231-652">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="c4231-653">Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.</span><span class="sxs-lookup"><span data-stu-id="c4231-653">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="c4231-654">`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c4231-654">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4231-655">Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="c4231-655">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="c4231-656">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="c4231-656">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="c4231-657">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="c4231-657">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="c4231-658">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="c4231-658">Dynamically select policies</span></span>

<span data-ttu-id="c4231-659">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="c4231-659">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="c4231-660">Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="c4231-660">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="c4231-661">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="c4231-661">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="c4231-662">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-662">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="c4231-663">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="c4231-663">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="c4231-664">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-664">Add multiple Polly handlers</span></span>

<span data-ttu-id="c4231-665">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="c4231-665">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="c4231-666">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-666">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="c4231-667">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="c4231-667">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="c4231-668">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="c4231-668">Failed requests are retried up to three times.</span></span> <span data-ttu-id="c4231-669">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-669">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="c4231-670">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="c4231-670">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="c4231-671">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="c4231-671">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="c4231-672">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="c4231-672">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="c4231-673">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-673">Add policies from the Polly registry</span></span>

<span data-ttu-id="c4231-674">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="c4231-674">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="c4231-675">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="c4231-675">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="c4231-676">V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="c4231-676">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="c4231-677">Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="c4231-677">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="c4231-678">Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="c4231-678">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="c4231-679">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c4231-679">HttpClient and lifetime management</span></span>

<span data-ttu-id="c4231-680">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-680">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-681">Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-681">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="c4231-682">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-682">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="c4231-683">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="c4231-683">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="c4231-684">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="c4231-684">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="c4231-685">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-685">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="c4231-686">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="c4231-686">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="c4231-687">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-687">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="c4231-688">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="c4231-688">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="c4231-689">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="c4231-689">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="c4231-690">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-690">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="c4231-691">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="c4231-691">Disposal of the client isn't required.</span></span> <span data-ttu-id="c4231-692">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="c4231-692">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="c4231-693">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-693">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="c4231-694">`HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="c4231-694">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="c4231-695">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-695">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="c4231-696">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="c4231-696">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="c4231-697">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c4231-697">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="c4231-698">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="c4231-698">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="c4231-699">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="c4231-699">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="c4231-700">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="c4231-700">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="c4231-701">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="c4231-701">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="c4231-702">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="c4231-702">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="c4231-703">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-703">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="c4231-704">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="c4231-704">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="c4231-705">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="c4231-705">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="c4231-706">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="c4231-706">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="c4231-707">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="c4231-707">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="c4231-708">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="c4231-708">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="c4231-709">Cookiepracují</span><span class="sxs-lookup"><span data-stu-id="c4231-709">Cookies</span></span>

<span data-ttu-id="c4231-710">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="c4231-710">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="c4231-711">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="c4231-711">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="c4231-712">U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="c4231-712">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="c4231-713">Zákaz automatického cookie zpracování</span><span class="sxs-lookup"><span data-stu-id="c4231-713">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="c4231-714">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="c4231-714">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="c4231-715">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:</span><span class="sxs-lookup"><span data-stu-id="c4231-715">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="c4231-716">protokolování</span><span class="sxs-lookup"><span data-stu-id="c4231-716">Logging</span></span>

<span data-ttu-id="c4231-717">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c4231-717">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="c4231-718">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="c4231-718">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="c4231-719">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="c4231-719">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="c4231-720">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-720">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="c4231-721">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="c4231-721">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="c4231-722">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-722">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="c4231-723">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-723">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="c4231-724">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="c4231-724">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="c4231-725">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="c4231-725">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="c4231-726">V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="c4231-726">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="c4231-727">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="c4231-727">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="c4231-728">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c4231-728">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="c4231-729">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="c4231-729">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="c4231-730">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c4231-730">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="c4231-731">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="c4231-731">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="c4231-732">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="c4231-732">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="c4231-733">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="c4231-733">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="c4231-734">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="c4231-734">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="c4231-735"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="c4231-735">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="c4231-736">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="c4231-736">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="c4231-737">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="c4231-737">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="c4231-738">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="c4231-738">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="c4231-739">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="c4231-739">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="c4231-740">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="c4231-740">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="c4231-741">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c4231-741">In the following example:</span></span>

* <span data-ttu-id="c4231-742"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="c4231-742"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="c4231-743">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="c4231-743">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="c4231-744">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="c4231-744">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="c4231-745">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="c4231-745">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="c4231-746">Middleware šíření hlaviček</span><span class="sxs-lookup"><span data-stu-id="c4231-746">Header propagation middleware</span></span>

<span data-ttu-id="c4231-747">Šíření hlaviček představuje middleware podporovanou komunitou pro šíření hlaviček protokolu HTTP z příchozího požadavku na odchozí požadavky klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4231-747">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="c4231-748">Použití rozšíření hlavičky:</span><span class="sxs-lookup"><span data-stu-id="c4231-748">To use header propagation:</span></span>

* <span data-ttu-id="c4231-749">Odkaz na podporovaný port komunity balíčku [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="c4231-749">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="c4231-750">ASP.NET Core 3,1 a novější podporuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="c4231-750">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="c4231-751">Konfigurace middlewaru a `HttpClient` v `Startup` :</span><span class="sxs-lookup"><span data-stu-id="c4231-751">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="c4231-752">Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:</span><span class="sxs-lookup"><span data-stu-id="c4231-752">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="c4231-753">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c4231-753">Additional resources</span></span>

* [<span data-ttu-id="c4231-754">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="c4231-754">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="c4231-755">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="c4231-755">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="c4231-756">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="c4231-756">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
