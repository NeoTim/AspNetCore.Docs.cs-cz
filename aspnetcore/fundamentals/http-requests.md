---
title: Vytváření požadavků HTTP pomocí aplikace IHttpClientFactory v ASP.NET jádru
author: stevejgordon
description: Přečtěte si o použití rozhraní IHttpClientFactory ke správě logických instancí httpclient u ASP.NET jádra.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661684"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="edbdf-103">Vytváření požadavků HTTP pomocí aplikace IHttpClientFactory v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="edbdf-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="edbdf-104">[Od Glenn condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="edbdf-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="edbdf-105">Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="edbdf-106">`IHttpClientFactory`nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="edbdf-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="edbdf-107">Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-108">Například klient s názvem *github* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="edbdf-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="edbdf-109">Výchozího klienta lze zaregistrovat pro obecný přístup.</span><span class="sxs-lookup"><span data-stu-id="edbdf-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="edbdf-110">Kodifikuje koncept odchozího middleware prostřednictvím delegování manipulátorů v `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="edbdf-111">Poskytuje rozšíření pro middleware založené na Polly, aby bylo `HttpClient`možné využít delegování obslužných rutin v .</span><span class="sxs-lookup"><span data-stu-id="edbdf-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="edbdf-112">Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="edbdf-113">Automatická správa se vyhýbá běžným problémům dns (Domain `HttpClient` Name System), ke kterým dochází při ruční správě životnosti.</span><span class="sxs-lookup"><span data-stu-id="edbdf-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="edbdf-114">Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="edbdf-115">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="edbdf-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="edbdf-116">Ukázkový kód v této <xref:System.Text.Json> verzi tématu používá k rekonstrukci obsahu JSON vrácenév odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="edbdf-117">Pro ukázky, které používají `Json.NET` a `ReadAsAsync<T>`, použijte volič verze pro výběr verze tohoto tématu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="edbdf-118">Spotřeby</span><span class="sxs-lookup"><span data-stu-id="edbdf-118">Consumption patterns</span></span>

<span data-ttu-id="edbdf-119">V aplikaci `IHttpClientFactory` lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="edbdf-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="edbdf-120">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="edbdf-121">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="edbdf-122">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="edbdf-123">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="edbdf-124">Nejlepší přístup závisí na požadavcích aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="edbdf-125">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-125">Basic usage</span></span>

<span data-ttu-id="edbdf-126">`IHttpClientFactory`lze zaregistrovat `AddHttpClient`na telefonním čísle :</span><span class="sxs-lookup"><span data-stu-id="edbdf-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="edbdf-127">Lze `IHttpClientFactory` požádat pomocí [vkládání závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="edbdf-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="edbdf-128">Následující kód `IHttpClientFactory` používá k `HttpClient` vytvoření instance:</span><span class="sxs-lookup"><span data-stu-id="edbdf-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="edbdf-129">Použití `IHttpClientFactory` jako v předchozím příkladu je dobrý způsob, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="edbdf-130">Nemá žádný vliv `HttpClient` na to, jak se používá.</span><span class="sxs-lookup"><span data-stu-id="edbdf-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="edbdf-131">V místech, kde `HttpClient` jsou instance vytvořeny v existující aplikaci, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="edbdf-132">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-132">Named clients</span></span>

<span data-ttu-id="edbdf-133">Jmenovaní klienti jsou dobrou volbou, když:</span><span class="sxs-lookup"><span data-stu-id="edbdf-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="edbdf-134">Aplikace vyžaduje mnoho různých `HttpClient`použití .</span><span class="sxs-lookup"><span data-stu-id="edbdf-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="edbdf-135">Mnoho `HttpClient`s mají jinou konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="edbdf-136">Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v :</span><span class="sxs-lookup"><span data-stu-id="edbdf-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="edbdf-137">V předchozím kódu je klient nakonfigurován pomocí:</span><span class="sxs-lookup"><span data-stu-id="edbdf-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="edbdf-138">Základní adresa `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="edbdf-139">Dvě záhlaví potřebné pro práci s rozhraním API GitHub.</span><span class="sxs-lookup"><span data-stu-id="edbdf-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="edbdf-140">Vytvořit klienta</span><span class="sxs-lookup"><span data-stu-id="edbdf-140">CreateClient</span></span>

<span data-ttu-id="edbdf-141">Pokaždé <xref:System.Net.Http.IHttpClientFactory.CreateClient*> se nazývá:</span><span class="sxs-lookup"><span data-stu-id="edbdf-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="edbdf-142">Je vytvořena `HttpClient` nová instance.</span><span class="sxs-lookup"><span data-stu-id="edbdf-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="edbdf-143">Akce konfigurace se nazývá.</span><span class="sxs-lookup"><span data-stu-id="edbdf-143">The configuration action is called.</span></span>

<span data-ttu-id="edbdf-144">Chcete-li vytvořit pojmenovaného klienta, přejděte jeho název do `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="edbdf-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="edbdf-145">V předchozím kódu požadavek není nutné zadat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbdf-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="edbdf-146">Kód může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="edbdf-147">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-147">Typed clients</span></span>

<span data-ttu-id="edbdf-148">Zadali klienti:</span><span class="sxs-lookup"><span data-stu-id="edbdf-148">Typed clients:</span></span>

* <span data-ttu-id="edbdf-149">Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="edbdf-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="edbdf-150">Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="edbdf-151">Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým .</span><span class="sxs-lookup"><span data-stu-id="edbdf-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="edbdf-152">Může být například použit jeden zadaný klient:</span><span class="sxs-lookup"><span data-stu-id="edbdf-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="edbdf-153">Pro jeden koncový bod back-endu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="edbdf-154">Zapouzdřit všechny logiky zabývající se koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="edbdf-155">Práce s DI a může být aplikován, pokud je to požadováno v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="edbdf-156">Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="edbdf-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="edbdf-157">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-157">In the preceding code:</span></span>

* <span data-ttu-id="edbdf-158">Konfigurace je přesunuta do zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="edbdf-159">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="edbdf-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="edbdf-160">Specifické metody rozhraní API mohou `HttpClient` být vytvořeny, které zveřejňují funkce.</span><span class="sxs-lookup"><span data-stu-id="edbdf-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="edbdf-161">Například `GetAspNetDocsIssues` metoda zapouzdřuje kód pro načtení otevřených problémů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="edbdf-162">Následující kód <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> volá `Startup.ConfigureServices` v zaregistrovat zadali třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="edbdf-163">Zadaný klient je registrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="edbdf-164">V předchozím kódu `AddHttpClient` registruje `GitHubService` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="edbdf-165">Tato registrace používá metodu výroby k:</span><span class="sxs-lookup"><span data-stu-id="edbdf-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="edbdf-166">Vytvořte instanci `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="edbdf-167">Vytvořte instanci `GitHubService`, předávání `HttpClient` v instanci jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="edbdf-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="edbdf-168">Zadaný klient může být injektován a spotřebován přímo:</span><span class="sxs-lookup"><span data-stu-id="edbdf-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="edbdf-169">Konfigurace pro zadaného klienta může být `Startup.ConfigureServices`zadána při registraci v , nikoli v konstruktoru zadaného klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="edbdf-170">Lze `HttpClient` zapouzdřit v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="edbdf-171">Spíše než vystavit jako vlastnost, definujte metodu, která volá `HttpClient` instanci interně:</span><span class="sxs-lookup"><span data-stu-id="edbdf-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="edbdf-172">V předchozím kódu `HttpClient` je uložen v soukromém poli.</span><span class="sxs-lookup"><span data-stu-id="edbdf-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="edbdf-173">Přístup k `HttpClient` is veřejnou `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="edbdf-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="edbdf-174">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-174">Generated clients</span></span>

<span data-ttu-id="edbdf-175">`IHttpClientFactory`lze použít v kombinaci s knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="edbdf-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="edbdf-176">Refit je knihovna REST pro rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="edbdf-177">Převádí rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edbdf-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="edbdf-178">Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="edbdf-179">Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="edbdf-180">Zadaný klient lze přidat pomocí Refit generovat implementaci:</span><span class="sxs-lookup"><span data-stu-id="edbdf-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="edbdf-181">Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:</span><span class="sxs-lookup"><span data-stu-id="edbdf-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="edbdf-182">Middleware odchozí žádosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-182">Outgoing request middleware</span></span>

<span data-ttu-id="edbdf-183">`HttpClient`má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="edbdf-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="edbdf-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="edbdf-185">Zjednodušuje definování obslužných rutin, které mají být aplikovány pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="edbdf-186">Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="edbdf-187">Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="edbdf-188">Tento vzor:</span><span class="sxs-lookup"><span data-stu-id="edbdf-188">This pattern:</span></span>

  * <span data-ttu-id="edbdf-189">Je podobný příchozí middleware potrubí v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edbdf-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="edbdf-190">Poskytuje mechanismus pro správu průřezových problémů týkajících se požadavků HTTP, například:</span><span class="sxs-lookup"><span data-stu-id="edbdf-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="edbdf-191">Mezipaměti</span><span class="sxs-lookup"><span data-stu-id="edbdf-191">caching</span></span>
    * <span data-ttu-id="edbdf-192">zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="edbdf-192">error handling</span></span>
    * <span data-ttu-id="edbdf-193">Serializace</span><span class="sxs-lookup"><span data-stu-id="edbdf-193">serialization</span></span>
    * <span data-ttu-id="edbdf-194">protokolování</span><span class="sxs-lookup"><span data-stu-id="edbdf-194">logging</span></span>

<span data-ttu-id="edbdf-195">Vytvoření delegující obslužné rutiny:</span><span class="sxs-lookup"><span data-stu-id="edbdf-195">To create a delegating handler:</span></span>

* <span data-ttu-id="edbdf-196">Odvodit z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="edbdf-197">Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="edbdf-198">Spusťte kód před předáním požadavku další obslužné rutině v kanálu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="edbdf-199">Předchozí kód zkontroluje, `X-API-KEY` zda je hlavička v požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="edbdf-200">Pokud `X-API-KEY` chybí, <xref:System.Net.HttpStatusCode.BadRequest> je vrácena.</span><span class="sxs-lookup"><span data-stu-id="edbdf-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="edbdf-201">Do konfigurace pro `HttpClient` : <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName></span><span class="sxs-lookup"><span data-stu-id="edbdf-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="edbdf-202">V předchozím kódu `ValidateHeaderHandler` je registrován s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="edbdf-203">Vytvoří `IHttpClientFactory` samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="edbdf-204">Obslužné rutiny mohou záviset na služby libovolného oboru.</span><span class="sxs-lookup"><span data-stu-id="edbdf-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="edbdf-205">Služby, které jsou závislé na jsou uvolněny při obslužné rutiny je uvolněna.</span><span class="sxs-lookup"><span data-stu-id="edbdf-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="edbdf-206">Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="edbdf-207">Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést.</span><span class="sxs-lookup"><span data-stu-id="edbdf-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="edbdf-208">Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:</span><span class="sxs-lookup"><span data-stu-id="edbdf-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="edbdf-209">Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:</span><span class="sxs-lookup"><span data-stu-id="edbdf-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="edbdf-210">Předejte data do obslužné rutiny pomocí [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="edbdf-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="edbdf-211">Slouží <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> k přístupu k aktuálnímu požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="edbdf-212">Vytvořte <xref:System.Threading.AsyncLocal`1> vlastní objekt úložiště pro předání dat.</span><span class="sxs-lookup"><span data-stu-id="edbdf-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="edbdf-213">Použití obslužných rutin založených na Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-213">Use Polly-based handlers</span></span>

<span data-ttu-id="edbdf-214">`IHttpClientFactory`integruje s knihovnou třetí [strany Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="edbdf-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="edbdf-215">Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="edbdf-216">Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="edbdf-217">Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-218">Rozšíření Polly podporují přidání obslužné rutiny založené na Polly klientům.</span><span class="sxs-lookup"><span data-stu-id="edbdf-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="edbdf-219">Polly vyžaduje balíček [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="edbdf-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="edbdf-220">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="edbdf-220">Handle transient faults</span></span>

<span data-ttu-id="edbdf-221">Chyby obvykle dojít, když externí volání HTTP jsou přechodné.</span><span class="sxs-lookup"><span data-stu-id="edbdf-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="edbdf-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="edbdf-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="edbdf-223">Zásady `AddTransientHttpErrorPolicy` nakonfigurované s popisovačem následující odpovědi:</span><span class="sxs-lookup"><span data-stu-id="edbdf-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="edbdf-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="edbdf-224">HTTP 5xx</span></span>
* <span data-ttu-id="edbdf-225">PROTOKOL HTTP 408</span><span class="sxs-lookup"><span data-stu-id="edbdf-225">HTTP 408</span></span>

<span data-ttu-id="edbdf-226">`AddTransientHttpErrorPolicy`poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="edbdf-227">V předchozím kódu je `WaitAndRetryAsync` definována zásada.</span><span class="sxs-lookup"><span data-stu-id="edbdf-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="edbdf-228">Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="edbdf-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="edbdf-229">Dynamicky vybrané zásady</span><span class="sxs-lookup"><span data-stu-id="edbdf-229">Dynamically select policies</span></span>

<span data-ttu-id="edbdf-230">Rozšiřující metody jsou k dispozici pro přidání polly <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>založené obslužné rutiny, například .</span><span class="sxs-lookup"><span data-stu-id="edbdf-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="edbdf-231">Následující `AddPolicyHandler` přetížení kontroluje požadavek rozhodnout, které zásady použít:</span><span class="sxs-lookup"><span data-stu-id="edbdf-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="edbdf-232">V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="edbdf-233">Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="edbdf-234">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="edbdf-235">Je běžné vnořit Polly politiky:</span><span class="sxs-lookup"><span data-stu-id="edbdf-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="edbdf-236">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-236">In the preceding example:</span></span>

* <span data-ttu-id="edbdf-237">Jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-237">Two handlers are added.</span></span>
* <span data-ttu-id="edbdf-238">První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="edbdf-239">Neúspěšné požadavky jsou opakovány až třikrát.</span><span class="sxs-lookup"><span data-stu-id="edbdf-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="edbdf-240">Druhé `AddTransientHttpErrorPolicy` volání přidá zásady jističe.</span><span class="sxs-lookup"><span data-stu-id="edbdf-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="edbdf-241">Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k 5 neúspěšným pokusům postupně.</span><span class="sxs-lookup"><span data-stu-id="edbdf-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="edbdf-242">Zásady jističe jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="edbdf-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="edbdf-243">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="edbdf-244">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="edbdf-245">Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s .</span><span class="sxs-lookup"><span data-stu-id="edbdf-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="edbdf-246">V následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-246">In the following code:</span></span>

* <span data-ttu-id="edbdf-247">"Pravidelné" a "dlouhé" police jsou přidány.</span><span class="sxs-lookup"><span data-stu-id="edbdf-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="edbdf-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>přidá "pravidelné" a "dlouhé" zásady z registru.</span><span class="sxs-lookup"><span data-stu-id="edbdf-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="edbdf-249">Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="edbdf-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="edbdf-250">HttpClient a správa životnosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="edbdf-251">Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-252">Je <xref:System.Net.Http.HttpMessageHandler> vytvořen pro pojmenované klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="edbdf-253">Továrna spravuje `HttpMessageHandler` životnostininstance.</span><span class="sxs-lookup"><span data-stu-id="edbdf-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="edbdf-254">`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="edbdf-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="edbdf-255">Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.</span><span class="sxs-lookup"><span data-stu-id="edbdf-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="edbdf-256">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="edbdf-257">Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="edbdf-258">Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="edbdf-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="edbdf-259">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="edbdf-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="edbdf-260">Výchozí hodnota může být přepsána na základě pojmenovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="edbdf-261">`HttpClient`instance lze obecně považovat za objekty .NET, které **nevyžadují** vyřazení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="edbdf-262">Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="edbdf-263">`IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="edbdf-264">Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem .</span><span class="sxs-lookup"><span data-stu-id="edbdf-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-265">Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .</span><span class="sxs-lookup"><span data-stu-id="edbdf-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="edbdf-266">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="edbdf-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="edbdf-267">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:</span><span class="sxs-lookup"><span data-stu-id="edbdf-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="edbdf-268">Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="edbdf-269">Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="edbdf-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="edbdf-270">Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.</span><span class="sxs-lookup"><span data-stu-id="edbdf-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="edbdf-271">Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="edbdf-272">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="edbdf-273">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="edbdf-274">Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="edbdf-275">Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-276">Toto sdílení zabraňuje vyčerpání soketu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="edbdf-277">Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="edbdf-278">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-278">Cookies</span></span>

<span data-ttu-id="edbdf-279">Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="edbdf-280">Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="edbdf-281">U aplikací, které vyžadují soubory cookie, zvažte buď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="edbdf-282">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="edbdf-283">Vyhnout`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="edbdf-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="edbdf-284">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="edbdf-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="edbdf-285">Protokolování</span><span class="sxs-lookup"><span data-stu-id="edbdf-285">Logging</span></span>

<span data-ttu-id="edbdf-286">Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="edbdf-287">Povolte příslušnou úroveň informací v konfiguraci protokolování, abyste viděli výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="edbdf-288">Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="edbdf-289">Kategorie protokolu použitá pro každého klienta obsahuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="edbdf-290">Klient s názvem *MyNamedClient*, například protokoluje zprávy s kategorií "System.Net.Http.HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="edbdf-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="edbdf-291">Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-292">Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="edbdf-293">Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.</span><span class="sxs-lookup"><span data-stu-id="edbdf-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="edbdf-294">Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-295">V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány s kategorií protokolu "System.Net.Http.Http.HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="edbdf-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="edbdf-296">Pro požadavek k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="edbdf-297">Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="edbdf-298">Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="edbdf-299">To může zahrnovat změny hlaviček požadavků nebo stavového kódu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="edbdf-300">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty.</span><span class="sxs-lookup"><span data-stu-id="edbdf-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="edbdf-301">Konfigurace httpmessagehandleru</span><span class="sxs-lookup"><span data-stu-id="edbdf-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="edbdf-302">Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="edbdf-303">Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena.</span><span class="sxs-lookup"><span data-stu-id="edbdf-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="edbdf-304">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="edbdf-305">Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="edbdf-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="edbdf-306">Použití ihttpclientfactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="edbdf-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="edbdf-307">V konzolové aplikaci přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="edbdf-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="edbdf-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="edbdf-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="edbdf-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="edbdf-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="edbdf-310">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-310">In the following example:</span></span>

* <span data-ttu-id="edbdf-311"><xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="edbdf-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="edbdf-312">`MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="edbdf-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="edbdf-313">`HttpClient`se používá k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="edbdf-314">`Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="edbdf-315">Middleware šíření záhlaví</span><span class="sxs-lookup"><span data-stu-id="edbdf-315">Header propagation middleware</span></span>

<span data-ttu-id="edbdf-316">Šíření záhlaví je middleware ASP.NET Core k šíření hlaviček HTTP z příchozího požadavku na odchozí požadavky klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="edbdf-317">Použití šíření záhlaví:</span><span class="sxs-lookup"><span data-stu-id="edbdf-317">To use header propagation:</span></span>

* <span data-ttu-id="edbdf-318">Odkaz na balíček [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)</span><span class="sxs-lookup"><span data-stu-id="edbdf-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="edbdf-319">Konfigurace middlewaru `HttpClient` `Startup`a v :</span><span class="sxs-lookup"><span data-stu-id="edbdf-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="edbdf-320">Klient obsahuje nakonfigurované hlavičky pro odchozí požadavky:</span><span class="sxs-lookup"><span data-stu-id="edbdf-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="edbdf-321">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edbdf-321">Additional resources</span></span>

* [<span data-ttu-id="edbdf-322">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="edbdf-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="edbdf-323">Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="edbdf-324">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="edbdf-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="edbdf-325">Serializace a deserializace zařízení JSON v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="edbdf-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="edbdf-326">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="edbdf-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="edbdf-327">Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="edbdf-328">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="edbdf-328">It offers the following benefits:</span></span>

* <span data-ttu-id="edbdf-329">Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-330">Například *klient githubu* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="edbdf-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="edbdf-331">Výchozího klienta lze zaregistrovat pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="edbdf-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="edbdf-332">Kodifikuje koncept odchozí middleware prostřednictvím delegování obslužné rutiny a `HttpClient` poskytuje rozšíření pro Polly-založené middleware využít.</span><span class="sxs-lookup"><span data-stu-id="edbdf-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="edbdf-333">Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.</span><span class="sxs-lookup"><span data-stu-id="edbdf-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="edbdf-334">Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="edbdf-335">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="edbdf-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="edbdf-336">Spotřeby</span><span class="sxs-lookup"><span data-stu-id="edbdf-336">Consumption patterns</span></span>

<span data-ttu-id="edbdf-337">V aplikaci `IHttpClientFactory` lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="edbdf-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="edbdf-338">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="edbdf-339">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="edbdf-340">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="edbdf-341">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="edbdf-342">Žádný z nich není striktně lepší než jiný.</span><span class="sxs-lookup"><span data-stu-id="edbdf-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="edbdf-343">Nejlepší přístup závisí na omezení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="edbdf-344">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-344">Basic usage</span></span>

<span data-ttu-id="edbdf-345">Lze `IHttpClientFactory` zaregistrovat voláním `AddHttpClient` metody rozšíření `IServiceCollection`na `Startup.ConfigureServices` , uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="edbdf-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="edbdf-346">Po registraci, kód `IHttpClientFactory` může přijmout služby kdekoli lze vložit [s vkládání závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="edbdf-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="edbdf-347">Slouží `IHttpClientFactory` k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="edbdf-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="edbdf-348">Použití `IHttpClientFactory` tímto způsobem je dobrý způsob, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="edbdf-349">To nemá žádný vliv `HttpClient` na způsob, jakým se používá.</span><span class="sxs-lookup"><span data-stu-id="edbdf-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="edbdf-350">V místech, kde `HttpClient` jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="edbdf-351">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-351">Named clients</span></span>

<span data-ttu-id="edbdf-352">Pokud aplikace vyžaduje mnoho `HttpClient`různých použití , každý s jinou konfigurací, možnost je použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="edbdf-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="edbdf-353">Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v .</span><span class="sxs-lookup"><span data-stu-id="edbdf-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="edbdf-354">V předchozím kódu `AddHttpClient` se nazývá, zadání názvu *github*.</span><span class="sxs-lookup"><span data-stu-id="edbdf-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="edbdf-355">Tento klient má některé&mdash;výchozí konfigurace použít konkrétně základní adresu a dvě hlavičky potřebné pro práci s Rozhraní matem GitHub.</span><span class="sxs-lookup"><span data-stu-id="edbdf-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="edbdf-356">Pokaždé, `CreateClient` když je volána, `HttpClient` je vytvořena nová instance a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="edbdf-357">Chcete-li spotřebovat pojmenovaného klienta, `CreateClient`může být parametr řetězce předán společnosti .</span><span class="sxs-lookup"><span data-stu-id="edbdf-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="edbdf-358">Zadejte název klienta, který má být vytvořen:</span><span class="sxs-lookup"><span data-stu-id="edbdf-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="edbdf-359">V předchozím kódu požadavek není nutné zadat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbdf-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="edbdf-360">Může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="edbdf-361">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-361">Typed clients</span></span>

<span data-ttu-id="edbdf-362">Zadali klienti:</span><span class="sxs-lookup"><span data-stu-id="edbdf-362">Typed clients:</span></span>

* <span data-ttu-id="edbdf-363">Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="edbdf-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="edbdf-364">Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="edbdf-365">Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým .</span><span class="sxs-lookup"><span data-stu-id="edbdf-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="edbdf-366">Například jeden zadaný klient může být použit pro jeden koncový bod back-endu a zapouzdřit všechny logiky zabývající se tímto koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="edbdf-367">Pracujte s DI a můžete se v aplikaci injektovat tam, kde je to požadováno.</span><span class="sxs-lookup"><span data-stu-id="edbdf-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="edbdf-368">Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="edbdf-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="edbdf-369">V předchozím kódu je konfigurace přesunuta do zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="edbdf-370">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="edbdf-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="edbdf-371">Je možné definovat metody specifické pro `HttpClient` rozhraní API, které zveřejňují funkce.</span><span class="sxs-lookup"><span data-stu-id="edbdf-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="edbdf-372">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování a analyzovat nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="edbdf-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="edbdf-373">Chcete-li zaregistrovat zadali <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, lze `Startup.ConfigureServices`použít metodu obecné rozšíření v rámci , určení zadané třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="edbdf-374">Zadaný klient je registrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="edbdf-375">Zadaný klient může být injektován a spotřebován přímo:</span><span class="sxs-lookup"><span data-stu-id="edbdf-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="edbdf-376">Pokud je to preferováno, konfigurace pro zadaného klienta může být zadána při registraci v `Startup.ConfigureServices`aplikace , nikoli v konstruktoru zadaného klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="edbdf-377">Je možné zcela zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="edbdf-378">Spíše než vystavení jako vlastnost, veřejné metody mohou být `HttpClient` poskytnuty, které volají instanci interně.</span><span class="sxs-lookup"><span data-stu-id="edbdf-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="edbdf-379">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="edbdf-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="edbdf-380">Veškerý přístup k externím `GetRepos` voláním prochází metodou.</span><span class="sxs-lookup"><span data-stu-id="edbdf-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="edbdf-381">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-381">Generated clients</span></span>

<span data-ttu-id="edbdf-382">`IHttpClientFactory`lze použít v kombinaci s jinými knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="edbdf-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="edbdf-383">Refit je knihovna REST pro rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="edbdf-384">Převádí rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edbdf-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="edbdf-385">Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="edbdf-386">Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="edbdf-387">Zadaný klient lze přidat pomocí Refit generovat implementaci:</span><span class="sxs-lookup"><span data-stu-id="edbdf-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="edbdf-388">Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:</span><span class="sxs-lookup"><span data-stu-id="edbdf-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="edbdf-389">Middleware odchozí žádosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-389">Outgoing request middleware</span></span>

<span data-ttu-id="edbdf-390">`HttpClient`již má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="edbdf-391">Usnadňuje `IHttpClientFactory` definování obslužné rutiny použít pro každý pojmenovaný klient.</span><span class="sxs-lookup"><span data-stu-id="edbdf-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="edbdf-392">Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="edbdf-393">Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="edbdf-394">Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edbdf-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="edbdf-395">Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="edbdf-396">Chcete-li vytvořit obslužnou rutinu, definujte třídu odvozenou z . <xref:System.Net.Http.DelegatingHandler></span><span class="sxs-lookup"><span data-stu-id="edbdf-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="edbdf-397">Přepište `SendAsync` metodu spuštění kódu před předáním požadavku další obslužné rutině v kanálu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="edbdf-398">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="edbdf-399">Zkontroluje, zda `X-API-KEY` byla do požadavku zahrnuta hlavička.</span><span class="sxs-lookup"><span data-stu-id="edbdf-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="edbdf-400">Pokud záhlaví chybí, může se vyhnout volání HTTP a vrátit vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="edbdf-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="edbdf-401">Během registrace lze do konfigurace pro `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="edbdf-402">Tento úkol je proveden pomocí <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>metod rozšíření na .</span><span class="sxs-lookup"><span data-stu-id="edbdf-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="edbdf-403">V předchozím kódu `ValidateHeaderHandler` je registrován s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="edbdf-404">Vytvoří `IHttpClientFactory` samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="edbdf-405">Obslužné rutiny mohou záviset na službách libovolného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="edbdf-406">Služby, které jsou závislé na jsou uvolněny při obslužné rutiny je uvolněna.</span><span class="sxs-lookup"><span data-stu-id="edbdf-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="edbdf-407">Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="edbdf-408">Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést.</span><span class="sxs-lookup"><span data-stu-id="edbdf-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="edbdf-409">Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:</span><span class="sxs-lookup"><span data-stu-id="edbdf-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="edbdf-410">Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:</span><span class="sxs-lookup"><span data-stu-id="edbdf-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="edbdf-411">Předávat data do `HttpRequestMessage.Properties`obslužné rutiny pomocí .</span><span class="sxs-lookup"><span data-stu-id="edbdf-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="edbdf-412">Slouží `IHttpContextAccessor` k přístupu k aktuálnímu požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="edbdf-413">Vytvořte `AsyncLocal` vlastní objekt úložiště pro předání dat.</span><span class="sxs-lookup"><span data-stu-id="edbdf-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="edbdf-414">Použití obslužných rutin založených na Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-414">Use Polly-based handlers</span></span>

<span data-ttu-id="edbdf-415">`IHttpClientFactory`integruje s populární knihovnou třetích stran nazvanou [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="edbdf-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="edbdf-416">Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="edbdf-417">Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="edbdf-418">Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-419">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="edbdf-419">The Polly extensions:</span></span>

* <span data-ttu-id="edbdf-420">Podporujte přidávání obslužných rutin založených na Polly klientům.</span><span class="sxs-lookup"><span data-stu-id="edbdf-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="edbdf-421">Lze použít po instalaci balíčku [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="edbdf-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="edbdf-422">Balíček není součástí sdíleného rámce ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edbdf-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="edbdf-423">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="edbdf-423">Handle transient faults</span></span>

<span data-ttu-id="edbdf-424">K nejběžnějším chybám dochází, když jsou externí volání HTTP přechodná.</span><span class="sxs-lookup"><span data-stu-id="edbdf-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="edbdf-425">Je zahrnuta `AddTransientHttpErrorPolicy` praktická metoda rozšíření, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="edbdf-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="edbdf-426">Zásady nakonfigurované s tímto popisovačem `HttpRequestException`metody rozšíření , odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="edbdf-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="edbdf-427">Rozšíření `AddTransientHttpErrorPolicy` lze použít `Startup.ConfigureServices`v rámci .</span><span class="sxs-lookup"><span data-stu-id="edbdf-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="edbdf-428">Rozšíření poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="edbdf-429">V předchozím kódu je `WaitAndRetryAsync` definována zásada.</span><span class="sxs-lookup"><span data-stu-id="edbdf-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="edbdf-430">Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="edbdf-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="edbdf-431">Dynamicky vybrané zásady</span><span class="sxs-lookup"><span data-stu-id="edbdf-431">Dynamically select policies</span></span>

<span data-ttu-id="edbdf-432">Existují další metody rozšíření, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="edbdf-433">Jedním z `AddPolicyHandler`takových rozšíření je , který má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="edbdf-434">Jedno přetížení umožňuje požadavek, který má být zkontrolován při definování zásad, které mají být aplikovány:</span><span class="sxs-lookup"><span data-stu-id="edbdf-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="edbdf-435">V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="edbdf-436">Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="edbdf-437">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="edbdf-438">Je běžné vnořit Polly zásady poskytovat rozšířené funkce:</span><span class="sxs-lookup"><span data-stu-id="edbdf-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="edbdf-439">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="edbdf-440">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="edbdf-441">Neúspěšné požadavky jsou opakovány až třikrát.</span><span class="sxs-lookup"><span data-stu-id="edbdf-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="edbdf-442">Druhé volání `AddTransientHttpErrorPolicy` přidá jistič zásady.</span><span class="sxs-lookup"><span data-stu-id="edbdf-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="edbdf-443">Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k pěti neúspěšným pokusům postupně.</span><span class="sxs-lookup"><span data-stu-id="edbdf-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="edbdf-444">Zásady jističe jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="edbdf-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="edbdf-445">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="edbdf-446">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="edbdf-447">Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s .</span><span class="sxs-lookup"><span data-stu-id="edbdf-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="edbdf-448">Je k dispozici metoda rozšíření, která umožňuje obslužnou rutinu přidat pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="edbdf-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="edbdf-449">V předchozím kódu jsou registrovány dvě `PolicyRegistry` zásady `ServiceCollection`při přidání do .</span><span class="sxs-lookup"><span data-stu-id="edbdf-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="edbdf-450">Chcete-li použít zásadu `AddPolicyHandlerFromRegistry` z registru, metoda se používá, předávání název zásady použít.</span><span class="sxs-lookup"><span data-stu-id="edbdf-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="edbdf-451">Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="edbdf-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="edbdf-452">HttpClient a správa životnosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="edbdf-453">Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-454">Je tu <xref:System.Net.Http.HttpMessageHandler> jeden z klientů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="edbdf-455">Továrna spravuje `HttpMessageHandler` životnostininstance.</span><span class="sxs-lookup"><span data-stu-id="edbdf-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="edbdf-456">`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="edbdf-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="edbdf-457">Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.</span><span class="sxs-lookup"><span data-stu-id="edbdf-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="edbdf-458">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="edbdf-459">Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="edbdf-460">Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="edbdf-461">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="edbdf-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="edbdf-462">Výchozí hodnota může být přepsána na základě pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="edbdf-463">Chcete-li jej <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> přepsat, `IHttpClientBuilder` volání, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="edbdf-464">Likvidace klienta není nutná.</span><span class="sxs-lookup"><span data-stu-id="edbdf-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="edbdf-465">Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="edbdf-466">`IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-467">Instance `HttpClient` lze obecně považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="edbdf-468">Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem .</span><span class="sxs-lookup"><span data-stu-id="edbdf-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-469">Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .</span><span class="sxs-lookup"><span data-stu-id="edbdf-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="edbdf-470">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="edbdf-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="edbdf-471">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:</span><span class="sxs-lookup"><span data-stu-id="edbdf-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="edbdf-472">Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="edbdf-473">Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="edbdf-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="edbdf-474">Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.</span><span class="sxs-lookup"><span data-stu-id="edbdf-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="edbdf-475">Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="edbdf-476">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="edbdf-477">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="edbdf-478">Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="edbdf-479">Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-480">Toto sdílení zabraňuje vyčerpání soketu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="edbdf-481">Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="edbdf-482">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-482">Cookies</span></span>

<span data-ttu-id="edbdf-483">Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="edbdf-484">Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="edbdf-485">U aplikací, které vyžadují soubory cookie, zvažte buď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="edbdf-486">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="edbdf-487">Vyhnout`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="edbdf-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="edbdf-488">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="edbdf-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="edbdf-489">Protokolování</span><span class="sxs-lookup"><span data-stu-id="edbdf-489">Logging</span></span>

<span data-ttu-id="edbdf-490">Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="edbdf-491">Povolte příslušnou úroveň informací v konfiguraci protokolování a zostřikněte výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="edbdf-492">Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="edbdf-493">Kategorie protokolu použitá pro každého klienta obsahuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="edbdf-494">Klient s názvem *MyNamedClient*, například protokoluje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`zprávy s kategorií .</span><span class="sxs-lookup"><span data-stu-id="edbdf-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="edbdf-495">Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-496">Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="edbdf-497">Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.</span><span class="sxs-lookup"><span data-stu-id="edbdf-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="edbdf-498">Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-499">V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`proti kategorii protokolu .</span><span class="sxs-lookup"><span data-stu-id="edbdf-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="edbdf-500">U požadavku k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku v síti.</span><span class="sxs-lookup"><span data-stu-id="edbdf-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="edbdf-501">Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="edbdf-502">Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="edbdf-503">To může zahrnovat například změny hlaviček požadavků nebo stavového kódu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="edbdf-504">Zahrnutí názvu klienta v kategorii protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="edbdf-505">Konfigurace httpmessagehandleru</span><span class="sxs-lookup"><span data-stu-id="edbdf-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="edbdf-506">Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="edbdf-507">Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena.</span><span class="sxs-lookup"><span data-stu-id="edbdf-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="edbdf-508">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="edbdf-509">Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="edbdf-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="edbdf-510">Použití ihttpclientfactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="edbdf-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="edbdf-511">V konzolové aplikaci přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="edbdf-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="edbdf-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="edbdf-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="edbdf-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="edbdf-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="edbdf-514">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-514">In the following example:</span></span>

* <span data-ttu-id="edbdf-515"><xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="edbdf-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="edbdf-516">`MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="edbdf-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="edbdf-517">`HttpClient`se používá k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="edbdf-518">`Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="edbdf-519">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edbdf-519">Additional resources</span></span>

* [<span data-ttu-id="edbdf-520">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="edbdf-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="edbdf-521">Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="edbdf-522">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="edbdf-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="edbdf-523">[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="edbdf-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="edbdf-524">Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="edbdf-525">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="edbdf-525">It offers the following benefits:</span></span>

* <span data-ttu-id="edbdf-526">Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-527">Například *klient githubu* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="edbdf-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="edbdf-528">Výchozího klienta lze zaregistrovat pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="edbdf-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="edbdf-529">Kodifikuje koncept odchozí middleware prostřednictvím delegování obslužné rutiny a `HttpClient` poskytuje rozšíření pro Polly-založené middleware využít.</span><span class="sxs-lookup"><span data-stu-id="edbdf-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="edbdf-530">Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.</span><span class="sxs-lookup"><span data-stu-id="edbdf-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="edbdf-531">Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="edbdf-532">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="edbdf-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="edbdf-533">Požadavky</span><span class="sxs-lookup"><span data-stu-id="edbdf-533">Prerequisites</span></span>

<span data-ttu-id="edbdf-534">Projekty zaměřené na rozhraní .NET Framework vyžadují instalaci balíčku [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="edbdf-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="edbdf-535">Projekty, které cílí na jádro .NET a odkazují na `Microsoft.Extensions.Http` [metabalíček Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app) již balíček obsahují.</span><span class="sxs-lookup"><span data-stu-id="edbdf-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="edbdf-536">Spotřeby</span><span class="sxs-lookup"><span data-stu-id="edbdf-536">Consumption patterns</span></span>

<span data-ttu-id="edbdf-537">V aplikaci `IHttpClientFactory` lze použít několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="edbdf-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="edbdf-538">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="edbdf-539">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="edbdf-540">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="edbdf-541">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="edbdf-542">Žádný z nich není striktně lepší než jiný.</span><span class="sxs-lookup"><span data-stu-id="edbdf-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="edbdf-543">Nejlepší přístup závisí na omezení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="edbdf-544">Základní použití</span><span class="sxs-lookup"><span data-stu-id="edbdf-544">Basic usage</span></span>

<span data-ttu-id="edbdf-545">Lze `IHttpClientFactory` zaregistrovat voláním `AddHttpClient` metody rozšíření `IServiceCollection`na `Startup.ConfigureServices` , uvnitř metody.</span><span class="sxs-lookup"><span data-stu-id="edbdf-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="edbdf-546">Po registraci, kód `IHttpClientFactory` může přijmout služby kdekoli lze vložit [s vkládání závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="edbdf-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="edbdf-547">Slouží `IHttpClientFactory` k vytvoření `HttpClient` instance:</span><span class="sxs-lookup"><span data-stu-id="edbdf-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="edbdf-548">Použití `IHttpClientFactory` tímto způsobem je dobrý způsob, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edbdf-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="edbdf-549">To nemá žádný vliv `HttpClient` na způsob, jakým se používá.</span><span class="sxs-lookup"><span data-stu-id="edbdf-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="edbdf-550">V místech, kde `HttpClient` jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="edbdf-551">Jmenovaní klienti</span><span class="sxs-lookup"><span data-stu-id="edbdf-551">Named clients</span></span>

<span data-ttu-id="edbdf-552">Pokud aplikace vyžaduje mnoho `HttpClient`různých použití , každý s jinou konfigurací, možnost je použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="edbdf-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="edbdf-553">Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v .</span><span class="sxs-lookup"><span data-stu-id="edbdf-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="edbdf-554">V předchozím kódu `AddHttpClient` se nazývá, zadání názvu *github*.</span><span class="sxs-lookup"><span data-stu-id="edbdf-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="edbdf-555">Tento klient má některé&mdash;výchozí konfigurace použít konkrétně základní adresu a dvě hlavičky potřebné pro práci s Rozhraní matem GitHub.</span><span class="sxs-lookup"><span data-stu-id="edbdf-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="edbdf-556">Pokaždé, `CreateClient` když je volána, `HttpClient` je vytvořena nová instance a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="edbdf-557">Chcete-li spotřebovat pojmenovaného klienta, `CreateClient`může být parametr řetězce předán společnosti .</span><span class="sxs-lookup"><span data-stu-id="edbdf-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="edbdf-558">Zadejte název klienta, který má být vytvořen:</span><span class="sxs-lookup"><span data-stu-id="edbdf-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="edbdf-559">V předchozím kódu požadavek není nutné zadat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="edbdf-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="edbdf-560">Může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="edbdf-561">Klienti zadali</span><span class="sxs-lookup"><span data-stu-id="edbdf-561">Typed clients</span></span>

<span data-ttu-id="edbdf-562">Zadali klienti:</span><span class="sxs-lookup"><span data-stu-id="edbdf-562">Typed clients:</span></span>

* <span data-ttu-id="edbdf-563">Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="edbdf-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="edbdf-564">Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="edbdf-565">Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým .</span><span class="sxs-lookup"><span data-stu-id="edbdf-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="edbdf-566">Například jeden zadaný klient může být použit pro jeden koncový bod back-endu a zapouzdřit všechny logiky zabývající se tímto koncovým bodem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="edbdf-567">Pracujte s DI a můžete se v aplikaci injektovat tam, kde je to požadováno.</span><span class="sxs-lookup"><span data-stu-id="edbdf-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="edbdf-568">Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="edbdf-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="edbdf-569">V předchozím kódu je konfigurace přesunuta do zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="edbdf-570">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="edbdf-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="edbdf-571">Je možné definovat metody specifické pro `HttpClient` rozhraní API, které zveřejňují funkce.</span><span class="sxs-lookup"><span data-stu-id="edbdf-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="edbdf-572">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování a analyzovat nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="edbdf-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="edbdf-573">Chcete-li zaregistrovat zadali <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, lze `Startup.ConfigureServices`použít metodu obecné rozšíření v rámci , určení zadané třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="edbdf-574">Zadaný klient je registrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="edbdf-575">Zadaný klient může být injektován a spotřebován přímo:</span><span class="sxs-lookup"><span data-stu-id="edbdf-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="edbdf-576">Pokud je to preferováno, konfigurace pro zadaného klienta může být zadána při registraci v `Startup.ConfigureServices`aplikace , nikoli v konstruktoru zadaného klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="edbdf-577">Je možné zcela zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="edbdf-578">Spíše než vystavení jako vlastnost, veřejné metody mohou být `HttpClient` poskytnuty, které volají instanci interně.</span><span class="sxs-lookup"><span data-stu-id="edbdf-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="edbdf-579">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="edbdf-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="edbdf-580">Veškerý přístup k externím `GetRepos` voláním prochází metodou.</span><span class="sxs-lookup"><span data-stu-id="edbdf-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="edbdf-581">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="edbdf-581">Generated clients</span></span>

<span data-ttu-id="edbdf-582">`IHttpClientFactory`lze použít v kombinaci s jinými knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="edbdf-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="edbdf-583">Refit je knihovna REST pro rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="edbdf-584">Převádí rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edbdf-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="edbdf-585">Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="edbdf-586">Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="edbdf-587">Zadaný klient lze přidat pomocí Refit generovat implementaci:</span><span class="sxs-lookup"><span data-stu-id="edbdf-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="edbdf-588">Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:</span><span class="sxs-lookup"><span data-stu-id="edbdf-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="edbdf-589">Middleware odchozí žádosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-589">Outgoing request middleware</span></span>

<span data-ttu-id="edbdf-590">`HttpClient`již má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="edbdf-591">Usnadňuje `IHttpClientFactory` definování obslužné rutiny použít pro každý pojmenovaný klient.</span><span class="sxs-lookup"><span data-stu-id="edbdf-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="edbdf-592">Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="edbdf-593">Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="edbdf-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="edbdf-594">Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edbdf-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="edbdf-595">Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="edbdf-596">Chcete-li vytvořit obslužnou rutinu, definujte třídu odvozenou z . <xref:System.Net.Http.DelegatingHandler></span><span class="sxs-lookup"><span data-stu-id="edbdf-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="edbdf-597">Přepište `SendAsync` metodu spuštění kódu před předáním požadavku další obslužné rutině v kanálu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="edbdf-598">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="edbdf-599">Zkontroluje, zda `X-API-KEY` byla do požadavku zahrnuta hlavička.</span><span class="sxs-lookup"><span data-stu-id="edbdf-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="edbdf-600">Pokud záhlaví chybí, může se vyhnout volání HTTP a vrátit vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="edbdf-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="edbdf-601">Během registrace lze do konfigurace pro `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="edbdf-602">Tento úkol je proveden pomocí <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>metod rozšíření na .</span><span class="sxs-lookup"><span data-stu-id="edbdf-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="edbdf-603">V předchozím kódu `ValidateHeaderHandler` je registrován s DI.</span><span class="sxs-lookup"><span data-stu-id="edbdf-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="edbdf-604">Obslužná rutina **musí** být registrována v DI jako přechodná služba, nikdy scoped.</span><span class="sxs-lookup"><span data-stu-id="edbdf-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="edbdf-605">Pokud je obslužná rutina registrována jako služba s vymezeným oborem a všechny služby, na kterých je obslužná rutina závislá, jsou na jedno použití:</span><span class="sxs-lookup"><span data-stu-id="edbdf-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="edbdf-606">Služby obslužné rutiny může být uvolněna před obslužná rutina přejde mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="edbdf-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="edbdf-607">Vyřazené služby obslužné rutiny způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="edbdf-608">Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="edbdf-609">Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést.</span><span class="sxs-lookup"><span data-stu-id="edbdf-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="edbdf-610">Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:</span><span class="sxs-lookup"><span data-stu-id="edbdf-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="edbdf-611">Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:</span><span class="sxs-lookup"><span data-stu-id="edbdf-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="edbdf-612">Předávat data do `HttpRequestMessage.Properties`obslužné rutiny pomocí .</span><span class="sxs-lookup"><span data-stu-id="edbdf-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="edbdf-613">Slouží `IHttpContextAccessor` k přístupu k aktuálnímu požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="edbdf-614">Vytvořte `AsyncLocal` vlastní objekt úložiště pro předání dat.</span><span class="sxs-lookup"><span data-stu-id="edbdf-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="edbdf-615">Použití obslužných rutin založených na Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-615">Use Polly-based handlers</span></span>

<span data-ttu-id="edbdf-616">`IHttpClientFactory`integruje s populární knihovnou třetích stran nazvanou [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="edbdf-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="edbdf-617">Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET.</span><span class="sxs-lookup"><span data-stu-id="edbdf-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="edbdf-618">Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="edbdf-619">Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-620">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="edbdf-620">The Polly extensions:</span></span>

* <span data-ttu-id="edbdf-621">Podporujte přidávání obslužných rutin založených na Polly klientům.</span><span class="sxs-lookup"><span data-stu-id="edbdf-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="edbdf-622">Lze použít po instalaci balíčku [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="edbdf-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="edbdf-623">Balíček není součástí sdíleného rámce ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="edbdf-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="edbdf-624">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="edbdf-624">Handle transient faults</span></span>

<span data-ttu-id="edbdf-625">K nejběžnějším chybám dochází, když jsou externí volání HTTP přechodná.</span><span class="sxs-lookup"><span data-stu-id="edbdf-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="edbdf-626">Je zahrnuta `AddTransientHttpErrorPolicy` praktická metoda rozšíření, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="edbdf-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="edbdf-627">Zásady nakonfigurované s tímto popisovačem `HttpRequestException`metody rozšíření , odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="edbdf-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="edbdf-628">Rozšíření `AddTransientHttpErrorPolicy` lze použít `Startup.ConfigureServices`v rámci .</span><span class="sxs-lookup"><span data-stu-id="edbdf-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="edbdf-629">Rozšíření poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="edbdf-630">V předchozím kódu je `WaitAndRetryAsync` definována zásada.</span><span class="sxs-lookup"><span data-stu-id="edbdf-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="edbdf-631">Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="edbdf-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="edbdf-632">Dynamicky vybrané zásady</span><span class="sxs-lookup"><span data-stu-id="edbdf-632">Dynamically select policies</span></span>

<span data-ttu-id="edbdf-633">Existují další metody rozšíření, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="edbdf-634">Jedním z `AddPolicyHandler`takových rozšíření je , který má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="edbdf-635">Jedno přetížení umožňuje požadavek, který má být zkontrolován při definování zásad, které mají být aplikovány:</span><span class="sxs-lookup"><span data-stu-id="edbdf-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="edbdf-636">V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="edbdf-637">Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="edbdf-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="edbdf-638">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="edbdf-639">Je běžné vnořit Polly zásady poskytovat rozšířené funkce:</span><span class="sxs-lookup"><span data-stu-id="edbdf-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="edbdf-640">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="edbdf-641">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="edbdf-642">Neúspěšné požadavky jsou opakovány až třikrát.</span><span class="sxs-lookup"><span data-stu-id="edbdf-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="edbdf-643">Druhé volání `AddTransientHttpErrorPolicy` přidá jistič zásady.</span><span class="sxs-lookup"><span data-stu-id="edbdf-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="edbdf-644">Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k pěti neúspěšným pokusům postupně.</span><span class="sxs-lookup"><span data-stu-id="edbdf-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="edbdf-645">Zásady jističe jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="edbdf-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="edbdf-646">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="edbdf-647">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="edbdf-648">Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s .</span><span class="sxs-lookup"><span data-stu-id="edbdf-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="edbdf-649">Je k dispozici metoda rozšíření, která umožňuje obslužnou rutinu přidat pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="edbdf-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="edbdf-650">V předchozím kódu jsou registrovány dvě `PolicyRegistry` zásady `ServiceCollection`při přidání do .</span><span class="sxs-lookup"><span data-stu-id="edbdf-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="edbdf-651">Chcete-li použít zásadu `AddPolicyHandlerFromRegistry` z registru, metoda se používá, předávání název zásady použít.</span><span class="sxs-lookup"><span data-stu-id="edbdf-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="edbdf-652">Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="edbdf-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="edbdf-653">HttpClient a správa životnosti</span><span class="sxs-lookup"><span data-stu-id="edbdf-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="edbdf-654">Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="edbdf-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-655">Je tu <xref:System.Net.Http.HttpMessageHandler> jeden z klientů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="edbdf-656">Továrna spravuje `HttpMessageHandler` životnostininstance.</span><span class="sxs-lookup"><span data-stu-id="edbdf-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="edbdf-657">`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="edbdf-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="edbdf-658">Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.</span><span class="sxs-lookup"><span data-stu-id="edbdf-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="edbdf-659">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="edbdf-660">Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="edbdf-661">Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="edbdf-662">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="edbdf-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="edbdf-663">Výchozí hodnota může být přepsána na základě pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="edbdf-664">Chcete-li jej <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> přepsat, `IHttpClientBuilder` volání, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="edbdf-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="edbdf-665">Likvidace klienta není nutná.</span><span class="sxs-lookup"><span data-stu-id="edbdf-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="edbdf-666">Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="edbdf-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="edbdf-667">`IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-668">Instance `HttpClient` lze obecně považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="edbdf-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="edbdf-669">Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem .</span><span class="sxs-lookup"><span data-stu-id="edbdf-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="edbdf-670">Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .</span><span class="sxs-lookup"><span data-stu-id="edbdf-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="edbdf-671">Alternativy k IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="edbdf-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="edbdf-672">Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:</span><span class="sxs-lookup"><span data-stu-id="edbdf-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="edbdf-673">Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="edbdf-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="edbdf-674">Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.</span><span class="sxs-lookup"><span data-stu-id="edbdf-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="edbdf-675">Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.</span><span class="sxs-lookup"><span data-stu-id="edbdf-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="edbdf-676">Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="edbdf-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="edbdf-677">Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="edbdf-678">Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="edbdf-679">Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.</span><span class="sxs-lookup"><span data-stu-id="edbdf-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="edbdf-680">Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="edbdf-681">Toto sdílení zabraňuje vyčerpání soketu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="edbdf-682">Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.</span><span class="sxs-lookup"><span data-stu-id="edbdf-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="edbdf-683">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-683">Cookies</span></span>

<span data-ttu-id="edbdf-684">Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů.</span><span class="sxs-lookup"><span data-stu-id="edbdf-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="edbdf-685">Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="edbdf-686">U aplikací, které vyžadují soubory cookie, zvažte buď:</span><span class="sxs-lookup"><span data-stu-id="edbdf-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="edbdf-687">Zakázání automatického zpracování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="edbdf-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="edbdf-688">Vyhnout`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="edbdf-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="edbdf-689">Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:</span><span class="sxs-lookup"><span data-stu-id="edbdf-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="edbdf-690">Protokolování</span><span class="sxs-lookup"><span data-stu-id="edbdf-690">Logging</span></span>

<span data-ttu-id="edbdf-691">Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="edbdf-692">Povolte příslušnou úroveň informací v konfiguraci protokolování a zostřikněte výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="edbdf-693">Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="edbdf-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="edbdf-694">Kategorie protokolu použitá pro každého klienta obsahuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="edbdf-695">Klient s názvem *MyNamedClient*, například protokoluje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`zprávy s kategorií .</span><span class="sxs-lookup"><span data-stu-id="edbdf-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="edbdf-696">Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-697">Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="edbdf-698">Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.</span><span class="sxs-lookup"><span data-stu-id="edbdf-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="edbdf-699">Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="edbdf-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="edbdf-700">V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`proti kategorii protokolu .</span><span class="sxs-lookup"><span data-stu-id="edbdf-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="edbdf-701">U požadavku k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku v síti.</span><span class="sxs-lookup"><span data-stu-id="edbdf-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="edbdf-702">Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="edbdf-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="edbdf-703">Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="edbdf-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="edbdf-704">To může zahrnovat například změny hlaviček požadavků nebo stavového kódu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="edbdf-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="edbdf-705">Zahrnutí názvu klienta v kategorii protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="edbdf-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="edbdf-706">Konfigurace httpmessagehandleru</span><span class="sxs-lookup"><span data-stu-id="edbdf-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="edbdf-707">Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="edbdf-708">Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena.</span><span class="sxs-lookup"><span data-stu-id="edbdf-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="edbdf-709">Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta.</span><span class="sxs-lookup"><span data-stu-id="edbdf-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="edbdf-710">Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="edbdf-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="edbdf-711">Použití ihttpclientfactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="edbdf-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="edbdf-712">V konzolové aplikaci přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="edbdf-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="edbdf-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="edbdf-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="edbdf-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="edbdf-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="edbdf-715">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edbdf-715">In the following example:</span></span>

* <span data-ttu-id="edbdf-716"><xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="edbdf-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="edbdf-717">`MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="edbdf-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="edbdf-718">`HttpClient`se používá k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="edbdf-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="edbdf-719">`Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="edbdf-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="edbdf-720">Middleware šíření záhlaví</span><span class="sxs-lookup"><span data-stu-id="edbdf-720">Header propagation middleware</span></span>

<span data-ttu-id="edbdf-721">Šíření záhlaví je komunitou podporovaný middleware k šíření hlaviček HTTP z příchozího požadavku na odchozí požadavky klienta HTTP.</span><span class="sxs-lookup"><span data-stu-id="edbdf-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="edbdf-722">Použití šíření záhlaví:</span><span class="sxs-lookup"><span data-stu-id="edbdf-722">To use header propagation:</span></span>

* <span data-ttu-id="edbdf-723">Odkaz na komunitu podporovaný port balíčku [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="edbdf-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="edbdf-724">ASP.NET Core 3.1 a novější podporuje [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="edbdf-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="edbdf-725">Konfigurace middlewaru `HttpClient` `Startup`a v :</span><span class="sxs-lookup"><span data-stu-id="edbdf-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="edbdf-726">Klient obsahuje nakonfigurované hlavičky pro odchozí požadavky:</span><span class="sxs-lookup"><span data-stu-id="edbdf-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="edbdf-727">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edbdf-727">Additional resources</span></span>

* [<span data-ttu-id="edbdf-728">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="edbdf-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="edbdf-729">Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="edbdf-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="edbdf-730">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="edbdf-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
