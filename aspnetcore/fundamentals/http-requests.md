---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766547"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="41364-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41364-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41364-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin</span><span class="sxs-lookup"><span data-stu-id="41364-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="41364-105">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="41364-106">`IHttpClientFactory`nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="41364-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="41364-107">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="41364-108">Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="41364-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="41364-109">Pro obecný přístup je možné zaregistrovat výchozího klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="41364-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="41364-111">Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="41364-112">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="41364-113">Automatická správa zabraňuje běžným problémům se službou DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="41364-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="41364-114">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="41364-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="41364-115">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="41364-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="41364-116">Vzorový kód v tomto tématu verze používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích http.</span><span class="sxs-lookup"><span data-stu-id="41364-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="41364-117">V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>` , použijte selektor verzí k výběru verze 2. x tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="41364-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="41364-118">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="41364-118">Consumption patterns</span></span>

<span data-ttu-id="41364-119">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="41364-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="41364-120">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="41364-121">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="41364-122">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="41364-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="41364-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="41364-125">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-125">Basic usage</span></span>

<span data-ttu-id="41364-126">`IHttpClientFactory`lze zaregistrovat voláním `AddHttpClient` :</span><span class="sxs-lookup"><span data-stu-id="41364-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="41364-127">`IHttpClientFactory`Může být požadováno pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="41364-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="41364-128">Následující kód používá `IHttpClientFactory` k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="41364-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="41364-129">Použití `IHttpClientFactory` Like v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="41364-130">Nemá žádný vliv na to, jak `HttpClient` se používá.</span><span class="sxs-lookup"><span data-stu-id="41364-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="41364-131">Na místech `HttpClient` , kde jsou vytvořeny instance v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="41364-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="41364-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-132">Named clients</span></span>

<span data-ttu-id="41364-133">Pojmenovaná klienti jsou dobrou volbou v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="41364-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="41364-134">Aplikace vyžaduje mnoho různých použití `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="41364-135">Mnoho `HttpClient` s má odlišnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="41364-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="41364-136">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="41364-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="41364-137">V předchozím kódu je klient nakonfigurován pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="41364-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="41364-138">Základní adresa `https://api.github.com/` .</span><span class="sxs-lookup"><span data-stu-id="41364-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="41364-139">Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.</span><span class="sxs-lookup"><span data-stu-id="41364-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="41364-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="41364-140">CreateClient</span></span>

<span data-ttu-id="41364-141">Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*> se zavolá:</span><span class="sxs-lookup"><span data-stu-id="41364-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="41364-142">Vytvoří se nová instance `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="41364-143">Je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="41364-143">The configuration action is called.</span></span>

<span data-ttu-id="41364-144">Chcete-li vytvořit pojmenovaného klienta, předejte jeho jméno do `CreateClient` :</span><span class="sxs-lookup"><span data-stu-id="41364-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="41364-145">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="41364-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="41364-146">Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="41364-147">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-147">Typed clients</span></span>

<span data-ttu-id="41364-148">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="41364-148">Typed clients:</span></span>

* <span data-ttu-id="41364-149">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="41364-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="41364-150">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="41364-151">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="41364-152">Můžete například použít jednoho typu klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="41364-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="41364-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="41364-154">Zapouzdřit veškerou práci s koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="41364-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="41364-155">Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="41364-156">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="41364-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="41364-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="41364-157">In the preceding code:</span></span>

* <span data-ttu-id="41364-158">Konfigurace je přesunuta do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="41364-159">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="41364-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="41364-160">Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkce.</span><span class="sxs-lookup"><span data-stu-id="41364-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="41364-161">Například `GetAspNetDocsIssues` Metoda zapouzdřuje kód pro načtení otevřených problémů.</span><span class="sxs-lookup"><span data-stu-id="41364-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="41364-162">Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="41364-163">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="41364-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="41364-164">V předchozím kódu se `AddHttpClient` registruje `GitHubService` jako dočasná služba.</span><span class="sxs-lookup"><span data-stu-id="41364-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="41364-165">Tato registrace používá metodu továrny k těmto účelům:</span><span class="sxs-lookup"><span data-stu-id="41364-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="41364-166">Vytvořte instanci `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="41364-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="41364-167">Vytvořte instanci objektu s `GitHubService` předáním v instanci `HttpClient` do jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="41364-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="41364-168">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="41364-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="41364-169">Konfiguraci pro typového klienta lze zadat během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="41364-170">`HttpClient`Může být zapouzdřen v rámci typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="41364-171">Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která volá `HttpClient` interně instanci:</span><span class="sxs-lookup"><span data-stu-id="41364-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="41364-172">V předchozím kódu `HttpClient` je uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="41364-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="41364-173">Přístup k aplikaci `HttpClient` je veřejný `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="41364-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="41364-174">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-174">Generated clients</span></span>

<span data-ttu-id="41364-175">`IHttpClientFactory`dá se použít v kombinaci s knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="41364-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="41364-176">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="41364-177">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41364-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="41364-178">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="41364-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="41364-179">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="41364-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="41364-180">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="41364-181">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="41364-182">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="41364-182">Outgoing request middleware</span></span>

<span data-ttu-id="41364-183">`HttpClient`má koncepci delegování obslužných rutin, které mohou být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="41364-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="41364-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="41364-185">Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="41364-186">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="41364-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="41364-187">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="41364-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="41364-188">Tento model:</span><span class="sxs-lookup"><span data-stu-id="41364-188">This pattern:</span></span>

  * <span data-ttu-id="41364-189">Se podobá příchozímu kanálu middleware v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41364-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="41364-190">Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:</span><span class="sxs-lookup"><span data-stu-id="41364-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="41364-191">vyrovnávací</span><span class="sxs-lookup"><span data-stu-id="41364-191">caching</span></span>
    * <span data-ttu-id="41364-192">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="41364-192">error handling</span></span>
    * <span data-ttu-id="41364-193">serializace</span><span class="sxs-lookup"><span data-stu-id="41364-193">serialization</span></span>
    * <span data-ttu-id="41364-194">protokolování</span><span class="sxs-lookup"><span data-stu-id="41364-194">logging</span></span>

<span data-ttu-id="41364-195">Vytvoření obslužné rutiny delegování:</span><span class="sxs-lookup"><span data-stu-id="41364-195">To create a delegating handler:</span></span>

* <span data-ttu-id="41364-196">Odvodit z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="41364-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="41364-197">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*> .</span><span class="sxs-lookup"><span data-stu-id="41364-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="41364-198">Před předáním požadavku další obslužné rutině v kanálu spusťte kód:</span><span class="sxs-lookup"><span data-stu-id="41364-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="41364-199">Předchozí kód zkontroluje, zda `X-API-KEY` je hlavička v žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="41364-200">Pokud `X-API-KEY` chybí, <xref:System.Net.HttpStatusCode.BadRequest> je vrácena.</span><span class="sxs-lookup"><span data-stu-id="41364-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="41364-201">Do konfigurace pro se dá přidat víc než jedna obslužná rutina `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="41364-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="41364-202">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="41364-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="41364-203">`IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="41364-204">Obslužné rutiny mohou záviset na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="41364-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="41364-205">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="41364-206">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="41364-207">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="41364-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="41364-208">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="41364-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="41364-209">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="41364-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="41364-210">Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="41364-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="41364-211"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="41364-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="41364-212">Vytvořte vlastní <xref:System.Threading.AsyncLocal`1> objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="41364-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="41364-213">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="41364-213">Use Polly-based handlers</span></span>

<span data-ttu-id="41364-214">`IHttpClientFactory`integruje se s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany.</span><span class="sxs-lookup"><span data-stu-id="41364-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="41364-215">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="41364-216">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="41364-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="41364-217">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="41364-218">Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="41364-219">Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="41364-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="41364-220">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="41364-220">Handle transient faults</span></span>

<span data-ttu-id="41364-221">K chybám obvykle dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="41364-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="41364-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="41364-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="41364-223">Zásady nakonfigurované s nástrojem `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="41364-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="41364-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="41364-224">HTTP 5xx</span></span>
* <span data-ttu-id="41364-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="41364-225">HTTP 408</span></span>

<span data-ttu-id="41364-226">`AddTransientHttpErrorPolicy`poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="41364-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="41364-227">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="41364-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="41364-228">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="41364-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="41364-229">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="41364-229">Dynamically select policies</span></span>

<span data-ttu-id="41364-230">Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> .</span><span class="sxs-lookup"><span data-stu-id="41364-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="41364-231">Následující `AddPolicyHandler` přetížení zkontroluje požadavek na rozhodnutí, které zásady se mají použít:</span><span class="sxs-lookup"><span data-stu-id="41364-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="41364-232">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="41364-233">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="41364-234">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="41364-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="41364-235">Je běžné vnořovat zásady Polly:</span><span class="sxs-lookup"><span data-stu-id="41364-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="41364-236">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41364-236">In the preceding example:</span></span>

* <span data-ttu-id="41364-237">Přidávají se dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-237">Two handlers are added.</span></span>
* <span data-ttu-id="41364-238">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="41364-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="41364-239">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="41364-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="41364-240">Druhé `AddTransientHttpErrorPolicy` volání přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="41364-241">Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="41364-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="41364-242">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="41364-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="41364-243">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="41364-244">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="41364-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="41364-245">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="41364-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="41364-246">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="41364-246">In the following code:</span></span>

* <span data-ttu-id="41364-247">Přidávají se zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="41364-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="41364-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>Přidá do registru zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="41364-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="41364-249">Další informace o `IHttpClientFactory` Polly integrech a integraci najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="41364-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="41364-250">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="41364-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="41364-251">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-252"><xref:System.Net.Http.HttpMessageHandler>Vytvoří se pro jednotlivé pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="41364-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="41364-253">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="41364-254">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="41364-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="41364-255">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="41364-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="41364-256">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="41364-257">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="41364-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="41364-258">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="41364-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="41364-259">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="41364-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="41364-260">Výchozí hodnota může být přepsána podle pojmenovaných klientů:</span><span class="sxs-lookup"><span data-stu-id="41364-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="41364-261">`HttpClient`instance je obecně možné považovat **za objekty .NET, které** nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="41364-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="41364-262">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="41364-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="41364-263">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="41364-264">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-265">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="41364-266">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="41364-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="41364-267">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="41364-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="41364-268">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="41364-269">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="41364-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="41364-270">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="41364-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="41364-271">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="41364-272">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="41364-273">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="41364-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="41364-274">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="41364-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="41364-275">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="41364-276">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="41364-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="41364-277">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="41364-278">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="41364-278">Cookies</span></span>

<span data-ttu-id="41364-279">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="41364-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="41364-280">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="41364-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="41364-281">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="41364-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="41364-282">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="41364-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="41364-283">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="41364-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="41364-284">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="41364-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="41364-285">Protokolování</span><span class="sxs-lookup"><span data-stu-id="41364-285">Logging</span></span>

<span data-ttu-id="41364-286">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="41364-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="41364-287">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="41364-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="41364-288">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="41364-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="41364-289">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="41364-290">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="41364-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="41364-291">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="41364-292">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="41364-293">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="41364-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="41364-294">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="41364-295">V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="41364-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="41364-296">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="41364-297">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="41364-298">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="41364-299">To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="41364-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="41364-300">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="41364-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="41364-301">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="41364-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="41364-302">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="41364-303">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="41364-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="41364-304"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="41364-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="41364-305">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="41364-306">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="41364-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="41364-307">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="41364-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="41364-308">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="41364-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="41364-309">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="41364-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="41364-310">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41364-310">In the following example:</span></span>

* <span data-ttu-id="41364-311"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="41364-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="41364-312">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="41364-313">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="41364-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="41364-314">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="41364-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="41364-315">Middleware šíření hlaviček</span><span class="sxs-lookup"><span data-stu-id="41364-315">Header propagation middleware</span></span>

<span data-ttu-id="41364-316">Šíření hlaviček je ASP.NET Core middleware pro šíření hlaviček protokolu HTTP z příchozího požadavku do odchozích požadavků klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="41364-317">Použití rozšíření hlavičky:</span><span class="sxs-lookup"><span data-stu-id="41364-317">To use header propagation:</span></span>

* <span data-ttu-id="41364-318">Odkázat na balíček [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="41364-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="41364-319">Konfigurace middlewaru a `HttpClient` v `Startup` :</span><span class="sxs-lookup"><span data-stu-id="41364-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="41364-320">Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:</span><span class="sxs-lookup"><span data-stu-id="41364-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="41364-321">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="41364-321">Additional resources</span></span>

* [<span data-ttu-id="41364-322">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="41364-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="41364-323">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="41364-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="41364-324">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="41364-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="41364-325">Postup serializace a deserializace JSON v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="41364-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="41364-326">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="41364-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="41364-327">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="41364-328">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="41364-328">It offers the following benefits:</span></span>

* <span data-ttu-id="41364-329">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="41364-330">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="41364-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="41364-331">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="41364-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="41364-332">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="41364-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="41364-333">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="41364-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="41364-334">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="41364-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="41364-335">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="41364-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="41364-336">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="41364-336">Consumption patterns</span></span>

<span data-ttu-id="41364-337">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="41364-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="41364-338">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="41364-339">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="41364-340">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="41364-341">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="41364-342">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="41364-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="41364-343">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="41364-344">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-344">Basic usage</span></span>

<span data-ttu-id="41364-345">`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="41364-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="41364-346">Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="41364-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="41364-347">`IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="41364-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="41364-348">`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="41364-349">Nemá žádný vliv na způsob `HttpClient` použití.</span><span class="sxs-lookup"><span data-stu-id="41364-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="41364-350">Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="41364-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="41364-351">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-351">Named clients</span></span>

<span data-ttu-id="41364-352">Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="41364-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="41364-353">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="41364-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="41364-354">V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="41364-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="41364-355">U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="41364-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="41364-356">Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="41364-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="41364-357">Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="41364-358">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="41364-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="41364-359">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="41364-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="41364-360">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="41364-361">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-361">Typed clients</span></span>

<span data-ttu-id="41364-362">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="41364-362">Typed clients:</span></span>

* <span data-ttu-id="41364-363">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="41364-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="41364-364">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="41364-365">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="41364-366">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="41364-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="41364-367">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="41364-368">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="41364-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="41364-369">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="41364-370">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="41364-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="41364-371">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost.</span><span class="sxs-lookup"><span data-stu-id="41364-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="41364-372">`GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="41364-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="41364-373">K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="41364-374">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="41364-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="41364-375">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="41364-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="41364-376">Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="41364-377">Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="41364-378">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.</span><span class="sxs-lookup"><span data-stu-id="41364-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="41364-379">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="41364-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="41364-380">Veškerý přístup k externím voláním prochází `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="41364-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="41364-381">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-381">Generated clients</span></span>

<span data-ttu-id="41364-382">`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="41364-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="41364-383">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="41364-384">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41364-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="41364-385">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="41364-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="41364-386">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="41364-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="41364-387">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="41364-388">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="41364-389">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="41364-389">Outgoing request middleware</span></span>

<span data-ttu-id="41364-390">`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="41364-391">`IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="41364-392">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="41364-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="41364-393">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="41364-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="41364-394">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41364-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="41364-395">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="41364-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="41364-396">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="41364-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="41364-397">`SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="41364-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="41364-398">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="41364-399">Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička.</span><span class="sxs-lookup"><span data-stu-id="41364-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="41364-400">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="41364-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="41364-401">Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="41364-402">Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="41364-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="41364-403">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="41364-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="41364-404">`IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="41364-405">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="41364-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="41364-406">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="41364-407">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="41364-408">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="41364-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="41364-409">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="41364-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="41364-410">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="41364-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="41364-411">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="41364-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="41364-412">`IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="41364-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="41364-413">Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="41364-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="41364-414">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="41364-414">Use Polly-based handlers</span></span>

<span data-ttu-id="41364-415">`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="41364-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="41364-416">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="41364-417">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="41364-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="41364-418">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="41364-419">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="41364-419">The Polly extensions:</span></span>

* <span data-ttu-id="41364-420">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="41364-421">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="41364-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="41364-422">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41364-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="41364-423">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="41364-423">Handle transient faults</span></span>

<span data-ttu-id="41364-424">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="41364-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="41364-425">`AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="41364-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="41364-426">Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.</span><span class="sxs-lookup"><span data-stu-id="41364-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="41364-427">`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="41364-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="41364-428">Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="41364-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="41364-429">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="41364-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="41364-430">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="41364-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="41364-431">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="41364-431">Dynamically select policies</span></span>

<span data-ttu-id="41364-432">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="41364-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="41364-433">Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="41364-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="41364-434">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="41364-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="41364-435">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="41364-436">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="41364-437">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="41364-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="41364-438">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="41364-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="41364-439">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="41364-440">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="41364-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="41364-441">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="41364-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="41364-442">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="41364-443">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="41364-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="41364-444">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="41364-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="41364-445">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="41364-446">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="41364-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="41364-447">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="41364-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="41364-448">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="41364-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="41364-449">V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="41364-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="41364-450">Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="41364-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="41364-451">Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="41364-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="41364-452">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="41364-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="41364-453">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-454">Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="41364-455">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="41364-456">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="41364-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="41364-457">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="41364-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="41364-458">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="41364-459">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="41364-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="41364-460">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="41364-461">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="41364-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="41364-462">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="41364-463">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="41364-464">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="41364-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="41364-465">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="41364-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="41364-466">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="41364-467">`HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="41364-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="41364-468">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-469">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="41364-470">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="41364-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="41364-471">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="41364-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="41364-472">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="41364-473">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="41364-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="41364-474">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="41364-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="41364-475">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="41364-476">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="41364-477">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="41364-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="41364-478">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="41364-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="41364-479">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="41364-480">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="41364-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="41364-481">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="41364-482">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="41364-482">Cookies</span></span>

<span data-ttu-id="41364-483">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="41364-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="41364-484">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="41364-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="41364-485">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="41364-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="41364-486">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="41364-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="41364-487">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="41364-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="41364-488">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="41364-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="41364-489">Protokolování</span><span class="sxs-lookup"><span data-stu-id="41364-489">Logging</span></span>

<span data-ttu-id="41364-490">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="41364-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="41364-491">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="41364-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="41364-492">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="41364-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="41364-493">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="41364-494">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="41364-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="41364-495">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="41364-496">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="41364-497">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="41364-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="41364-498">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="41364-499">V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="41364-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="41364-500">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="41364-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="41364-501">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="41364-502">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="41364-503">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="41364-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="41364-504">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="41364-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="41364-505">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="41364-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="41364-506">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="41364-507">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="41364-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="41364-508"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="41364-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="41364-509">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="41364-510">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="41364-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="41364-511">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="41364-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="41364-512">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="41364-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="41364-513">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="41364-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="41364-514">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41364-514">In the following example:</span></span>

* <span data-ttu-id="41364-515"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="41364-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="41364-516">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="41364-517">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="41364-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="41364-518">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="41364-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="41364-519">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="41364-519">Additional resources</span></span>

* [<span data-ttu-id="41364-520">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="41364-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="41364-521">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="41364-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="41364-522">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="41364-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="41364-523">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="41364-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="41364-524">Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="41364-525">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="41364-525">It offers the following benefits:</span></span>

* <span data-ttu-id="41364-526">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="41364-527">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="41364-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="41364-528">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="41364-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="41364-529">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="41364-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="41364-530">Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="41364-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="41364-531">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="41364-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="41364-532">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="41364-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="41364-533">Požadavky</span><span class="sxs-lookup"><span data-stu-id="41364-533">Prerequisites</span></span>

<span data-ttu-id="41364-534">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="41364-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="41364-535">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už `Microsoft.Extensions.Http` balíček obsahuje.</span><span class="sxs-lookup"><span data-stu-id="41364-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="41364-536">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="41364-536">Consumption patterns</span></span>

<span data-ttu-id="41364-537">`IHttpClientFactory`V aplikaci lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="41364-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="41364-538">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="41364-539">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="41364-540">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="41364-541">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="41364-542">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="41364-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="41364-543">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="41364-544">Základní použití</span><span class="sxs-lookup"><span data-stu-id="41364-544">Basic usage</span></span>

<span data-ttu-id="41364-545">`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="41364-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="41364-546">Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="41364-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="41364-547">`IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="41364-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="41364-548">`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="41364-549">Nemá žádný vliv na způsob `HttpClient` použití.</span><span class="sxs-lookup"><span data-stu-id="41364-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="41364-550">Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .</span><span class="sxs-lookup"><span data-stu-id="41364-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="41364-551">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="41364-551">Named clients</span></span>

<span data-ttu-id="41364-552">Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="41364-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="41364-553">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="41364-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="41364-554">V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="41364-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="41364-555">U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="41364-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="41364-556">Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="41364-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="41364-557">Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="41364-558">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="41364-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="41364-559">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="41364-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="41364-560">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="41364-561">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="41364-561">Typed clients</span></span>

<span data-ttu-id="41364-562">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="41364-562">Typed clients:</span></span>

* <span data-ttu-id="41364-563">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="41364-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="41364-564">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="41364-565">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="41364-566">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="41364-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="41364-567">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="41364-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="41364-568">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="41364-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="41364-569">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="41364-570">`HttpClient`Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="41364-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="41364-571">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost.</span><span class="sxs-lookup"><span data-stu-id="41364-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="41364-572">`GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="41364-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="41364-573">K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="41364-574">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="41364-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="41364-575">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="41364-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="41364-576">Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="41364-577">Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="41364-578">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.</span><span class="sxs-lookup"><span data-stu-id="41364-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="41364-579">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="41364-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="41364-580">Veškerý přístup k externím voláním prochází `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="41364-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="41364-581">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="41364-581">Generated clients</span></span>

<span data-ttu-id="41364-582">`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="41364-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="41364-583">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="41364-584">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41364-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="41364-585">Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="41364-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="41364-586">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="41364-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="41364-587">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="41364-588">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="41364-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="41364-589">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="41364-589">Outgoing request middleware</span></span>

<span data-ttu-id="41364-590">`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="41364-591">`IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="41364-592">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="41364-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="41364-593">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="41364-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="41364-594">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="41364-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="41364-595">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="41364-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="41364-596">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> .</span><span class="sxs-lookup"><span data-stu-id="41364-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="41364-597">`SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="41364-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="41364-598">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="41364-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="41364-599">Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička.</span><span class="sxs-lookup"><span data-stu-id="41364-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="41364-600">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="41364-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="41364-601">Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="41364-602">Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .</span><span class="sxs-lookup"><span data-stu-id="41364-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="41364-603">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="41364-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="41364-604">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="41364-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="41364-605">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="41364-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="41364-606">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="41364-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="41364-607">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="41364-608">Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné je volat s předáním do typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="41364-609">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="41364-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="41364-610">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="41364-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="41364-611">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="41364-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="41364-612">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .</span><span class="sxs-lookup"><span data-stu-id="41364-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="41364-613">`IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="41364-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="41364-614">Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="41364-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="41364-615">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="41364-615">Use Polly-based handlers</span></span>

<span data-ttu-id="41364-616">`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="41364-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="41364-617">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="41364-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="41364-618">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="41364-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="41364-619">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="41364-620">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="41364-620">The Polly extensions:</span></span>

* <span data-ttu-id="41364-621">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="41364-622">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="41364-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="41364-623">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="41364-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="41364-624">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="41364-624">Handle transient faults</span></span>

<span data-ttu-id="41364-625">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="41364-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="41364-626">`AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="41364-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="41364-627">Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.</span><span class="sxs-lookup"><span data-stu-id="41364-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="41364-628">`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="41364-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="41364-629">Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="41364-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="41364-630">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="41364-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="41364-631">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="41364-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="41364-632">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="41364-632">Dynamically select policies</span></span>

<span data-ttu-id="41364-633">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="41364-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="41364-634">Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="41364-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="41364-635">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="41364-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="41364-636">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="41364-637">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="41364-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="41364-638">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="41364-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="41364-639">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="41364-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="41364-640">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="41364-641">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="41364-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="41364-642">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="41364-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="41364-643">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="41364-644">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="41364-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="41364-645">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="41364-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="41364-646">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="41364-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="41364-647">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="41364-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="41364-648">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .</span><span class="sxs-lookup"><span data-stu-id="41364-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="41364-649">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="41364-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="41364-650">V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` .</span><span class="sxs-lookup"><span data-stu-id="41364-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="41364-651">Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="41364-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="41364-652">Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="41364-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="41364-653">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="41364-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="41364-654">Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-655">Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="41364-656">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="41364-657">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="41364-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="41364-658">Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="41364-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="41364-659">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="41364-660">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="41364-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="41364-661">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="41364-662">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="41364-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="41364-663">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="41364-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="41364-664">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="41364-665">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="41364-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="41364-666">Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> .</span><span class="sxs-lookup"><span data-stu-id="41364-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="41364-667">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="41364-668">`HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="41364-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="41364-669">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="41364-670">Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="41364-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="41364-671">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="41364-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="41364-672">Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:</span><span class="sxs-lookup"><span data-stu-id="41364-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="41364-673">Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="41364-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="41364-674">Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="41364-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="41364-675">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.</span><span class="sxs-lookup"><span data-stu-id="41364-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="41364-676">Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="41364-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="41364-677">Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="41364-678">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="41364-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="41364-679">Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.</span><span class="sxs-lookup"><span data-stu-id="41364-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="41364-680">`SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="41364-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="41364-681">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="41364-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="41364-682">`SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.</span><span class="sxs-lookup"><span data-stu-id="41364-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="41364-683">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="41364-683">Cookies</span></span>

<span data-ttu-id="41364-684">Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="41364-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="41364-685">Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="41364-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="41364-686">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="41364-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="41364-687">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="41364-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="41364-688">Opakované`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="41364-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="41364-689">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="41364-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="41364-690">Protokolování</span><span class="sxs-lookup"><span data-stu-id="41364-690">Logging</span></span>

<span data-ttu-id="41364-691">Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="41364-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="41364-692">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="41364-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="41364-693">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="41364-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="41364-694">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="41364-695">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` .</span><span class="sxs-lookup"><span data-stu-id="41364-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="41364-696">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="41364-697">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="41364-698">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="41364-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="41364-699">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="41364-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="41364-700">V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="41364-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="41364-701">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="41364-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="41364-702">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="41364-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="41364-703">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="41364-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="41364-704">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="41364-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="41364-705">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="41364-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="41364-706">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="41364-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="41364-707">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="41364-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="41364-708">`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí.</span><span class="sxs-lookup"><span data-stu-id="41364-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="41364-709"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="41364-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="41364-710">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="41364-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="41364-711">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="41364-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="41364-712">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="41364-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="41364-713">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="41364-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="41364-714">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="41364-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="41364-715">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="41364-715">In the following example:</span></span>

* <span data-ttu-id="41364-716"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="41364-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="41364-717">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="41364-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="41364-718">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="41364-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="41364-719">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="41364-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="41364-720">Middleware šíření hlaviček</span><span class="sxs-lookup"><span data-stu-id="41364-720">Header propagation middleware</span></span>

<span data-ttu-id="41364-721">Šíření hlaviček představuje middleware podporovanou komunitou pro šíření hlaviček protokolu HTTP z příchozího požadavku na odchozí požadavky klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="41364-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="41364-722">Použití rozšíření hlavičky:</span><span class="sxs-lookup"><span data-stu-id="41364-722">To use header propagation:</span></span>

* <span data-ttu-id="41364-723">Odkaz na podporovaný port komunity balíčku [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="41364-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="41364-724">ASP.NET Core 3,1 a novější podporuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="41364-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="41364-725">Konfigurace middlewaru a `HttpClient` v `Startup` :</span><span class="sxs-lookup"><span data-stu-id="41364-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="41364-726">Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:</span><span class="sxs-lookup"><span data-stu-id="41364-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="41364-727">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="41364-727">Additional resources</span></span>

* [<span data-ttu-id="41364-728">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="41364-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="41364-729">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="41364-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="41364-730">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="41364-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
