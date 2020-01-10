---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 482f8e28c23c621cecaf9ce111d89e9166ea6d85
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722723"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="57d61-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57d61-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57d61-104">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin</span><span class="sxs-lookup"><span data-stu-id="57d61-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="57d61-105"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="57d61-106">`IHttpClientFactory` nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="57d61-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="57d61-107">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="57d61-108">Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="57d61-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="57d61-109">Pro obecný přístup je možné zaregistrovat výchozího klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="57d61-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="57d61-111">Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="57d61-112">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="57d61-113">Automatická správa zabraňuje běžným problémům DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient`ch dob života.</span><span class="sxs-lookup"><span data-stu-id="57d61-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="57d61-114">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="57d61-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="57d61-115">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="57d61-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="57d61-116">Vzorový kód v této verzi tématu používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="57d61-117">V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>`použijte selektor verzí k výběru verze 2. x tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="57d61-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="57d61-118">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="57d61-118">Consumption patterns</span></span>

<span data-ttu-id="57d61-119">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="57d61-120">Základní využití</span><span class="sxs-lookup"><span data-stu-id="57d61-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="57d61-121">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="57d61-122">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="57d61-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="57d61-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="57d61-125">Základní použití</span><span class="sxs-lookup"><span data-stu-id="57d61-125">Basic usage</span></span>

<span data-ttu-id="57d61-126">`IHttpClientFactory` lze zaregistrovat voláním `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="57d61-127">`IHttpClientFactory` lze požadovat pomocí [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="57d61-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57d61-128">Následující kód používá `IHttpClientFactory` k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="57d61-129">Použití `IHttpClientFactory` jako v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="57d61-130">Nemá žádný vliv na to, jak se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="57d61-131">V místech, kde jsou `HttpClient` instance vytvořené v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="57d61-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-132">Named clients</span></span>

<span data-ttu-id="57d61-133">Pojmenovaná klienti jsou dobrou volbou v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="57d61-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="57d61-134">Aplikace vyžaduje mnoho různých použití `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="57d61-135">Mnoho `HttpClient`s má odlišnou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="57d61-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="57d61-136">Konfigurace pro pojmenovanou `HttpClient` se dá zadat během registrace v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="57d61-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="57d61-137">V předchozím kódu je klient nakonfigurován pomocí nástroje:</span><span class="sxs-lookup"><span data-stu-id="57d61-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="57d61-138">Základní adresa `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="57d61-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="57d61-139">Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.</span><span class="sxs-lookup"><span data-stu-id="57d61-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="57d61-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="57d61-140">CreateClient</span></span>

<span data-ttu-id="57d61-141">Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*>, je volána:</span><span class="sxs-lookup"><span data-stu-id="57d61-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="57d61-142">Vytvoří se nová instance `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="57d61-143">Je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="57d61-143">The configuration action is called.</span></span>

<span data-ttu-id="57d61-144">Chcete-li vytvořit pojmenovaného klienta, předejte jeho název do `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="57d61-145">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="57d61-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="57d61-146">Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="57d61-147">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-147">Typed clients</span></span>

<span data-ttu-id="57d61-148">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="57d61-148">Typed clients:</span></span>

* <span data-ttu-id="57d61-149">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="57d61-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="57d61-150">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="57d61-151">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="57d61-152">Můžete například použít jednoho typu klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="57d61-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="57d61-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="57d61-154">Zapouzdřit veškerou práci s koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="57d61-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="57d61-155">Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="57d61-156">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="57d61-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="57d61-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="57d61-157">In the preceding code:</span></span>

* <span data-ttu-id="57d61-158">Konfigurace je přesunuta do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="57d61-159">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="57d61-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="57d61-160">Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="57d61-161">Například metoda `GetAspNetDocsIssues` zapouzdřuje kód, aby načetla otevřené problémy.</span><span class="sxs-lookup"><span data-stu-id="57d61-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="57d61-162">Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="57d61-163">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="57d61-164">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="57d61-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="57d61-165">Konfigurace pro zadaného klienta se dá zadat během registrace v `Startup.ConfigureServices`místo v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="57d61-166">`HttpClient` lze zapouzdřit v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="57d61-167">Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která interně volá instanci `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="57d61-168">V předchozím kódu je `HttpClient` uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="57d61-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="57d61-169">Přístup k `HttpClient` je veřejným `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="57d61-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="57d61-170">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-170">Generated clients</span></span>

<span data-ttu-id="57d61-171">`IHttpClientFactory` lze použít v kombinaci s knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="57d61-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="57d61-172">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="57d61-173">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57d61-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="57d61-174">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="57d61-175">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="57d61-175">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="57d61-176">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-176">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="57d61-177">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="57d61-178">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="57d61-178">Outgoing request middleware</span></span>

<span data-ttu-id="57d61-179">`HttpClient` má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="57d61-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="57d61-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="57d61-181">Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="57d61-182">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="57d61-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="57d61-183">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="57d61-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="57d61-184">Tento model:</span><span class="sxs-lookup"><span data-stu-id="57d61-184">This pattern:</span></span>

  * <span data-ttu-id="57d61-185">Se podobá příchozímu kanálu middleware v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57d61-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="57d61-186">Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:</span><span class="sxs-lookup"><span data-stu-id="57d61-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="57d61-187">ukládání do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="57d61-187">caching</span></span>
    * <span data-ttu-id="57d61-188">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="57d61-188">error handling</span></span>
    * <span data-ttu-id="57d61-189">serializace</span><span class="sxs-lookup"><span data-stu-id="57d61-189">serialization</span></span>
    * <span data-ttu-id="57d61-190">protokolování</span><span class="sxs-lookup"><span data-stu-id="57d61-190">logging</span></span>

<span data-ttu-id="57d61-191">Vytvoření obslužné rutiny delegování:</span><span class="sxs-lookup"><span data-stu-id="57d61-191">To create a delegating handler:</span></span>

* <span data-ttu-id="57d61-192">Odvodit z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="57d61-193">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="57d61-194">Před předáním požadavku další obslužné rutině v kanálu spusťte kód:</span><span class="sxs-lookup"><span data-stu-id="57d61-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="57d61-195">Předchozí kód zkontroluje, zda je hlavička `X-API-KEY` v žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="57d61-196">Pokud chybí `X-API-KEY`, vrátí se <xref:System.Net.HttpStatusCode.BadRequest>.</span><span class="sxs-lookup"><span data-stu-id="57d61-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="57d61-197">Do konfigurace pro `HttpClient` se dá přidat víc než jedna obslužná rutina s <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="57d61-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="57d61-198">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="57d61-199">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="57d61-200">Obslužné rutiny mohou záviset na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="57d61-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="57d61-201">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="57d61-202">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="57d61-203">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="57d61-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="57d61-204">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="57d61-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="57d61-205">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57d61-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="57d61-206">Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="57d61-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="57d61-207">Pro přístup k aktuálnímu požadavku použijte <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="57d61-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="57d61-208">Vytvořte vlastní objekt úložiště <xref:System.Threading.AsyncLocal`1>, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="57d61-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="57d61-209">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-209">Use Polly-based handlers</span></span>

<span data-ttu-id="57d61-210">`IHttpClientFactory` se integruje s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany.</span><span class="sxs-lookup"><span data-stu-id="57d61-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="57d61-211">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="57d61-212">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="57d61-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="57d61-213">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="57d61-214">Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="57d61-215">Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="57d61-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="57d61-216">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="57d61-216">Handle transient faults</span></span>

<span data-ttu-id="57d61-217">K chybám obvykle dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="57d61-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="57d61-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="57d61-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="57d61-219">Zásady nakonfigurované pomocí `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="57d61-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="57d61-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="57d61-220">HTTP 5xx</span></span>
* <span data-ttu-id="57d61-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="57d61-221">HTTP 408</span></span>

<span data-ttu-id="57d61-222">`AddTransientHttpErrorPolicy` poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="57d61-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="57d61-223">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="57d61-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="57d61-224">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="57d61-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="57d61-225">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="57d61-225">Dynamically select policies</span></span>

<span data-ttu-id="57d61-226">Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="57d61-227">Následující `AddPolicyHandler` Overload zkontroluje požadavek na rozhodnutí, které zásady se mají použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="57d61-228">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="57d61-229">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="57d61-230">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="57d61-231">Je běžné vnořovat zásady Polly:</span><span class="sxs-lookup"><span data-stu-id="57d61-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="57d61-232">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="57d61-232">In the preceding example:</span></span>

* <span data-ttu-id="57d61-233">Přidávají se dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-233">Two handlers are added.</span></span>
* <span data-ttu-id="57d61-234">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="57d61-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="57d61-235">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="57d61-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="57d61-236">Druhý `AddTransientHttpErrorPolicy` volání přidá zásadu dělení na okruhy.</span><span class="sxs-lookup"><span data-stu-id="57d61-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="57d61-237">Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně.</span><span class="sxs-lookup"><span data-stu-id="57d61-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="57d61-238">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="57d61-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="57d61-239">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="57d61-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="57d61-240">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="57d61-241">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="57d61-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="57d61-242">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="57d61-242">In the following code:</span></span>

* <span data-ttu-id="57d61-243">Přidávají se zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="57d61-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="57d61-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> přidá do registru zásady "regular" a "Long".</span><span class="sxs-lookup"><span data-stu-id="57d61-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="57d61-245">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="57d61-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="57d61-246">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="57d61-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="57d61-247">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-248">Vytvoří se <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="57d61-249">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="57d61-250">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="57d61-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="57d61-251">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="57d61-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="57d61-252">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="57d61-253">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="57d61-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="57d61-254">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="57d61-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="57d61-255">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="57d61-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="57d61-256">Výchozí hodnota může být přepsána podle pojmenovaných klientů:</span><span class="sxs-lookup"><span data-stu-id="57d61-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="57d61-257">instance `HttpClient` můžou být obecně ošetřené **jako objekty .NET, které** nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="57d61-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="57d61-258">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="57d61-259">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="57d61-260">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-261">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="57d61-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="57d61-262">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="57d61-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="57d61-263">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="57d61-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="57d61-264">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="57d61-265">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="57d61-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="57d61-266">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="57d61-267">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="57d61-268">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="57d61-269">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="57d61-269">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="57d61-270">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="57d61-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="57d61-271">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="57d61-272">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="57d61-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="57d61-273">`SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="57d61-274">Cookies</span><span class="sxs-lookup"><span data-stu-id="57d61-274">Cookies</span></span>

<span data-ttu-id="57d61-275">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="57d61-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="57d61-276">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="57d61-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="57d61-277">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="57d61-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="57d61-278">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="57d61-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="57d61-279">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="57d61-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="57d61-280">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="57d61-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="57d61-281">protokolování</span><span class="sxs-lookup"><span data-stu-id="57d61-281">Logging</span></span>

<span data-ttu-id="57d61-282">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="57d61-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="57d61-283">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="57d61-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="57d61-284">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="57d61-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="57d61-285">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="57d61-286">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="57d61-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="57d61-287">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="57d61-288">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="57d61-289">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="57d61-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="57d61-290">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="57d61-291">V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="57d61-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="57d61-292">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="57d61-293">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="57d61-294">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="57d61-295">To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="57d61-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="57d61-296">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="57d61-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="57d61-297">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="57d61-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="57d61-298">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="57d61-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="57d61-299">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="57d61-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="57d61-300">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="57d61-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="57d61-301">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="57d61-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="57d61-302">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="57d61-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="57d61-303">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="57d61-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="57d61-304">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="57d61-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="57d61-305">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="57d61-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="57d61-306">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="57d61-306">In the following example:</span></span>

* <span data-ttu-id="57d61-307"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="57d61-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="57d61-308">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="57d61-309">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="57d61-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="57d61-310">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="57d61-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="57d61-311">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="57d61-311">Additional resources</span></span>

* [<span data-ttu-id="57d61-312">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="57d61-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="57d61-313">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="57d61-314">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="57d61-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="57d61-315">Postup serializace a deserializace JSON v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="57d61-315">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="57d61-316">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="57d61-316">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="57d61-317"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-317">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="57d61-318">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="57d61-318">It offers the following benefits:</span></span>

* <span data-ttu-id="57d61-319">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-319">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="57d61-320">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="57d61-320">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="57d61-321">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="57d61-321">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="57d61-322">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="57d61-322">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="57d61-323">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-323">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="57d61-324">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="57d61-324">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="57d61-325">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57d61-325">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="57d61-326">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="57d61-326">Consumption patterns</span></span>

<span data-ttu-id="57d61-327">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-327">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="57d61-328">Základní využití</span><span class="sxs-lookup"><span data-stu-id="57d61-328">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="57d61-329">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-329">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="57d61-330">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-330">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="57d61-331">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-331">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="57d61-332">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="57d61-332">None of them are strictly superior to another.</span></span> <span data-ttu-id="57d61-333">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-333">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="57d61-334">Základní použití</span><span class="sxs-lookup"><span data-stu-id="57d61-334">Basic usage</span></span>

<span data-ttu-id="57d61-335">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-335">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="57d61-336">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="57d61-336">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57d61-337">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-337">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="57d61-338">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-338">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="57d61-339">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-339">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="57d61-340">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-340">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="57d61-341">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-341">Named clients</span></span>

<span data-ttu-id="57d61-342">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="57d61-342">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="57d61-343">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-343">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="57d61-344">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="57d61-344">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="57d61-345">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="57d61-345">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="57d61-346">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="57d61-346">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="57d61-347">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-347">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="57d61-348">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="57d61-348">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="57d61-349">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="57d61-349">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="57d61-350">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-350">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="57d61-351">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-351">Typed clients</span></span>

<span data-ttu-id="57d61-352">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="57d61-352">Typed clients:</span></span>

* <span data-ttu-id="57d61-353">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="57d61-353">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="57d61-354">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-354">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="57d61-355">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-355">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="57d61-356">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="57d61-356">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="57d61-357">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-357">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="57d61-358">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="57d61-358">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="57d61-359">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-359">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="57d61-360">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="57d61-360">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="57d61-361">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-361">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="57d61-362">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="57d61-362">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="57d61-363">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-363">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="57d61-364">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-364">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="57d61-365">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="57d61-365">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="57d61-366">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-366">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="57d61-367">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-367">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="57d61-368">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="57d61-368">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="57d61-369">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="57d61-369">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="57d61-370">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="57d61-370">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="57d61-371">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-371">Generated clients</span></span>

<span data-ttu-id="57d61-372">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="57d61-372">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="57d61-373">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-373">Refit is a REST library for .NET.</span></span> <span data-ttu-id="57d61-374">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57d61-374">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="57d61-375">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-375">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="57d61-376">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="57d61-376">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="57d61-377">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-377">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="57d61-378">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-378">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="57d61-379">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="57d61-379">Outgoing request middleware</span></span>

<span data-ttu-id="57d61-380">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-380">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="57d61-381">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-381">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="57d61-382">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="57d61-382">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="57d61-383">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="57d61-383">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="57d61-384">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57d61-384">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="57d61-385">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="57d61-385">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="57d61-386">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-386">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="57d61-387">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="57d61-387">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="57d61-388">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-388">The preceding code defines a basic handler.</span></span> <span data-ttu-id="57d61-389">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="57d61-389">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="57d61-390">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="57d61-390">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="57d61-391">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="57d61-391">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="57d61-392">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="57d61-392">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="57d61-393">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-393">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="57d61-394">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-394">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="57d61-395">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="57d61-395">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="57d61-396">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-396">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="57d61-397">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-397">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="57d61-398">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="57d61-398">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="57d61-399">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="57d61-399">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="57d61-400">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57d61-400">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="57d61-401">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="57d61-401">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="57d61-402">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="57d61-402">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="57d61-403">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="57d61-403">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="57d61-404">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-404">Use Polly-based handlers</span></span>

<span data-ttu-id="57d61-405">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="57d61-405">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="57d61-406">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-406">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="57d61-407">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="57d61-407">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="57d61-408">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-408">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="57d61-409">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="57d61-409">The Polly extensions:</span></span>

* <span data-ttu-id="57d61-410">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-410">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="57d61-411">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="57d61-411">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="57d61-412">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57d61-412">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="57d61-413">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="57d61-413">Handle transient faults</span></span>

<span data-ttu-id="57d61-414">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="57d61-414">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="57d61-415">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="57d61-415">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="57d61-416">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="57d61-416">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="57d61-417">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-417">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57d61-418">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="57d61-418">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="57d61-419">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="57d61-419">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="57d61-420">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="57d61-420">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="57d61-421">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="57d61-421">Dynamically select policies</span></span>

<span data-ttu-id="57d61-422">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="57d61-422">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="57d61-423">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="57d61-423">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="57d61-424">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-424">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="57d61-425">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-425">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="57d61-426">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-426">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="57d61-427">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-427">Add multiple Polly handlers</span></span>

<span data-ttu-id="57d61-428">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="57d61-428">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="57d61-429">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-429">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="57d61-430">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="57d61-430">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="57d61-431">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="57d61-431">Failed requests are retried up to three times.</span></span> <span data-ttu-id="57d61-432">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="57d61-432">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="57d61-433">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="57d61-433">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="57d61-434">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="57d61-434">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="57d61-435">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="57d61-435">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="57d61-436">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-436">Add policies from the Polly registry</span></span>

<span data-ttu-id="57d61-437">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="57d61-437">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="57d61-438">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="57d61-438">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="57d61-439">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="57d61-439">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="57d61-440">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="57d61-440">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="57d61-441">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="57d61-441">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="57d61-442">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="57d61-442">HttpClient and lifetime management</span></span>

<span data-ttu-id="57d61-443">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-443">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-444">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-444">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="57d61-445">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-445">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="57d61-446">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="57d61-446">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="57d61-447">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="57d61-447">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="57d61-448">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-448">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="57d61-449">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="57d61-449">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="57d61-450">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-450">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="57d61-451">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="57d61-451">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="57d61-452">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-452">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="57d61-453">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-453">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="57d61-454">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="57d61-454">Disposal of the client isn't required.</span></span> <span data-ttu-id="57d61-455">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-455">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="57d61-456">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-456">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="57d61-457">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="57d61-457">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="57d61-458">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-458">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-459">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="57d61-459">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="57d61-460">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="57d61-460">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="57d61-461">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="57d61-461">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="57d61-462">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-462">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="57d61-463">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="57d61-463">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="57d61-464">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-464">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="57d61-465">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-465">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="57d61-466">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-466">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="57d61-467">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="57d61-467">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="57d61-468">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="57d61-468">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="57d61-469">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-469">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="57d61-470">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="57d61-470">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="57d61-471">`SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-471">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="57d61-472">Cookies</span><span class="sxs-lookup"><span data-stu-id="57d61-472">Cookies</span></span>

<span data-ttu-id="57d61-473">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="57d61-473">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="57d61-474">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="57d61-474">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="57d61-475">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="57d61-475">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="57d61-476">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="57d61-476">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="57d61-477">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="57d61-477">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="57d61-478">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="57d61-478">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="57d61-479">protokolování</span><span class="sxs-lookup"><span data-stu-id="57d61-479">Logging</span></span>

<span data-ttu-id="57d61-480">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="57d61-480">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="57d61-481">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="57d61-481">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="57d61-482">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="57d61-482">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="57d61-483">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-483">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="57d61-484">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-484">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="57d61-485">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-485">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="57d61-486">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-486">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="57d61-487">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="57d61-487">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="57d61-488">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-488">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="57d61-489">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-489">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="57d61-490">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="57d61-490">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="57d61-491">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-491">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="57d61-492">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-492">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="57d61-493">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="57d61-493">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="57d61-494">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="57d61-494">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="57d61-495">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="57d61-495">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="57d61-496">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="57d61-496">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="57d61-497">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="57d61-497">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="57d61-498">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="57d61-498">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="57d61-499">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="57d61-499">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="57d61-500">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="57d61-500">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="57d61-501">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="57d61-501">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="57d61-502">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="57d61-502">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="57d61-503">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="57d61-503">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="57d61-504">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="57d61-504">In the following example:</span></span>

* <span data-ttu-id="57d61-505"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="57d61-505"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="57d61-506">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-506">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="57d61-507">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="57d61-507">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="57d61-508">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="57d61-508">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="57d61-509">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="57d61-509">Additional resources</span></span>

* [<span data-ttu-id="57d61-510">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="57d61-510">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="57d61-511">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-511">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="57d61-512">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="57d61-512">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="57d61-513">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="57d61-513">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="57d61-514"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-514">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="57d61-515">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="57d61-515">It offers the following benefits:</span></span>

* <span data-ttu-id="57d61-516">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-516">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="57d61-517">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="57d61-517">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="57d61-518">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="57d61-518">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="57d61-519">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="57d61-519">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="57d61-520">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-520">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="57d61-521">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="57d61-521">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="57d61-522">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57d61-522">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57d61-523">Požadavky</span><span class="sxs-lookup"><span data-stu-id="57d61-523">Prerequisites</span></span>

<span data-ttu-id="57d61-524">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="57d61-524">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="57d61-525">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už obsahují balíček `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="57d61-525">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="57d61-526">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="57d61-526">Consumption patterns</span></span>

<span data-ttu-id="57d61-527">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-527">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="57d61-528">Základní využití</span><span class="sxs-lookup"><span data-stu-id="57d61-528">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="57d61-529">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-529">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="57d61-530">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-530">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="57d61-531">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-531">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="57d61-532">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="57d61-532">None of them are strictly superior to another.</span></span> <span data-ttu-id="57d61-533">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-533">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="57d61-534">Základní použití</span><span class="sxs-lookup"><span data-stu-id="57d61-534">Basic usage</span></span>

<span data-ttu-id="57d61-535">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-535">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="57d61-536">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="57d61-536">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57d61-537">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="57d61-537">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="57d61-538">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-538">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="57d61-539">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-539">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="57d61-540">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-540">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="57d61-541">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-541">Named clients</span></span>

<span data-ttu-id="57d61-542">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="57d61-542">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="57d61-543">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-543">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="57d61-544">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="57d61-544">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="57d61-545">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="57d61-545">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="57d61-546">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="57d61-546">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="57d61-547">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-547">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="57d61-548">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="57d61-548">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="57d61-549">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="57d61-549">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="57d61-550">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-550">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="57d61-551">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="57d61-551">Typed clients</span></span>

<span data-ttu-id="57d61-552">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="57d61-552">Typed clients:</span></span>

* <span data-ttu-id="57d61-553">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="57d61-553">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="57d61-554">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-554">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="57d61-555">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-555">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="57d61-556">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="57d61-556">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="57d61-557">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57d61-557">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="57d61-558">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="57d61-558">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="57d61-559">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-559">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="57d61-560">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="57d61-560">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="57d61-561">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-561">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="57d61-562">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="57d61-562">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="57d61-563">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-563">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="57d61-564">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-564">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="57d61-565">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="57d61-565">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="57d61-566">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-566">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="57d61-567">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-567">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="57d61-568">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="57d61-568">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="57d61-569">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="57d61-569">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="57d61-570">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="57d61-570">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="57d61-571">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="57d61-571">Generated clients</span></span>

<span data-ttu-id="57d61-572">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="57d61-572">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="57d61-573">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-573">Refit is a REST library for .NET.</span></span> <span data-ttu-id="57d61-574">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57d61-574">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="57d61-575">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-575">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="57d61-576">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="57d61-576">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="57d61-577">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-577">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="57d61-578">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="57d61-578">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="57d61-579">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="57d61-579">Outgoing request middleware</span></span>

<span data-ttu-id="57d61-580">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-580">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="57d61-581">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-581">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="57d61-582">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="57d61-582">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="57d61-583">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="57d61-583">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="57d61-584">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57d61-584">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="57d61-585">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="57d61-585">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="57d61-586">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-586">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="57d61-587">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="57d61-587">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="57d61-588">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="57d61-588">The preceding code defines a basic handler.</span></span> <span data-ttu-id="57d61-589">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="57d61-589">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="57d61-590">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="57d61-590">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="57d61-591">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="57d61-591">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="57d61-592">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="57d61-592">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="57d61-593">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="57d61-593">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="57d61-594">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="57d61-594">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="57d61-595">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="57d61-595">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="57d61-596">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="57d61-596">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="57d61-597">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-597">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="57d61-598">Po zaregistrování můžete <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> volat a předat do něj typ obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-598">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="57d61-599">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="57d61-599">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="57d61-600">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="57d61-600">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="57d61-601">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57d61-601">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="57d61-602">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="57d61-602">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="57d61-603">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="57d61-603">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="57d61-604">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="57d61-604">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="57d61-605">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-605">Use Polly-based handlers</span></span>

<span data-ttu-id="57d61-606">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="57d61-606">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="57d61-607">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="57d61-607">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="57d61-608">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="57d61-608">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="57d61-609">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-609">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="57d61-610">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="57d61-610">The Polly extensions:</span></span>

* <span data-ttu-id="57d61-611">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-611">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="57d61-612">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="57d61-612">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="57d61-613">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57d61-613">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="57d61-614">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="57d61-614">Handle transient faults</span></span>

<span data-ttu-id="57d61-615">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="57d61-615">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="57d61-616">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="57d61-616">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="57d61-617">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="57d61-617">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="57d61-618">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="57d61-618">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="57d61-619">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="57d61-619">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="57d61-620">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="57d61-620">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="57d61-621">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="57d61-621">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="57d61-622">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="57d61-622">Dynamically select policies</span></span>

<span data-ttu-id="57d61-623">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="57d61-623">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="57d61-624">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="57d61-624">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="57d61-625">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="57d61-625">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="57d61-626">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-626">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="57d61-627">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="57d61-627">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="57d61-628">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-628">Add multiple Polly handlers</span></span>

<span data-ttu-id="57d61-629">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="57d61-629">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="57d61-630">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-630">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="57d61-631">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="57d61-631">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="57d61-632">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="57d61-632">Failed requests are retried up to three times.</span></span> <span data-ttu-id="57d61-633">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="57d61-633">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="57d61-634">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="57d61-634">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="57d61-635">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="57d61-635">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="57d61-636">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="57d61-636">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="57d61-637">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-637">Add policies from the Polly registry</span></span>

<span data-ttu-id="57d61-638">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="57d61-638">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="57d61-639">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="57d61-639">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="57d61-640">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="57d61-640">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="57d61-641">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="57d61-641">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="57d61-642">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="57d61-642">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="57d61-643">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="57d61-643">HttpClient and lifetime management</span></span>

<span data-ttu-id="57d61-644">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-644">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-645">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-645">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="57d61-646">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-646">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="57d61-647">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="57d61-647">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="57d61-648">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="57d61-648">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="57d61-649">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="57d61-649">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="57d61-650">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="57d61-650">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="57d61-651">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-651">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="57d61-652">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="57d61-652">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="57d61-653">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="57d61-653">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="57d61-654">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="57d61-654">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="57d61-655">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="57d61-655">Disposal of the client isn't required.</span></span> <span data-ttu-id="57d61-656">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="57d61-656">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="57d61-657">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-657">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="57d61-658">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="57d61-658">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="57d61-659">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="57d61-659">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="57d61-660">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="57d61-660">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="57d61-661">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="57d61-661">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="57d61-662">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:</span><span class="sxs-lookup"><span data-stu-id="57d61-662">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="57d61-663">Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="57d61-663">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="57d61-664">Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="57d61-664">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="57d61-665">Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.</span><span class="sxs-lookup"><span data-stu-id="57d61-665">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="57d61-666">Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="57d61-666">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="57d61-667">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-667">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="57d61-668">Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="57d61-668">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="57d61-669">Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="57d61-669">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="57d61-670">`SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="57d61-670">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="57d61-671">Toto sdílení zabraňuje vyčerpání soketů.</span><span class="sxs-lookup"><span data-stu-id="57d61-671">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="57d61-672">`SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.</span><span class="sxs-lookup"><span data-stu-id="57d61-672">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="57d61-673">Cookies</span><span class="sxs-lookup"><span data-stu-id="57d61-673">Cookies</span></span>

<span data-ttu-id="57d61-674">Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`.</span><span class="sxs-lookup"><span data-stu-id="57d61-674">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="57d61-675">Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="57d61-675">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="57d61-676">U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="57d61-676">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="57d61-677">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="57d61-677">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="57d61-678">Předcházení `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="57d61-678">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="57d61-679">Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="57d61-679">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="57d61-680">protokolování</span><span class="sxs-lookup"><span data-stu-id="57d61-680">Logging</span></span>

<span data-ttu-id="57d61-681">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="57d61-681">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="57d61-682">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="57d61-682">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="57d61-683">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="57d61-683">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="57d61-684">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="57d61-684">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="57d61-685">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-685">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="57d61-686">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-686">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="57d61-687">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-687">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="57d61-688">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="57d61-688">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="57d61-689">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="57d61-689">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="57d61-690">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="57d61-690">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="57d61-691">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="57d61-691">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="57d61-692">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="57d61-692">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="57d61-693">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="57d61-693">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="57d61-694">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="57d61-694">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="57d61-695">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="57d61-695">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="57d61-696">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="57d61-696">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="57d61-697">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="57d61-697">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="57d61-698">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="57d61-698">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="57d61-699">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="57d61-699">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="57d61-700">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="57d61-700">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="57d61-701">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="57d61-701">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="57d61-702">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="57d61-702">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="57d61-703">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="57d61-703">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="57d61-704">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="57d61-704">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="57d61-705">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="57d61-705">In the following example:</span></span>

* <span data-ttu-id="57d61-706"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="57d61-706"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="57d61-707">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="57d61-707">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="57d61-708">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="57d61-708">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="57d61-709">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="57d61-709">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="57d61-710">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="57d61-710">Additional resources</span></span>

* [<span data-ttu-id="57d61-711">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="57d61-711">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="57d61-712">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="57d61-712">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="57d61-713">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="57d61-713">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
