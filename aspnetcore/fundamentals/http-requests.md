---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/01/2019
uid: fundamentals/http-requests
ms.openlocfilehash: d29814f0b057119aaa68a7435879d04987ca0b0b
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034175"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="02dd0-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02dd0-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02dd0-104">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="02dd0-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

[!INCLUDE[](~/includes/not30.md)]

<span data-ttu-id="02dd0-105"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02dd0-106">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="02dd0-106">It offers the following benefits:</span></span>

* <span data-ttu-id="02dd0-107">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="02dd0-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-108">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02dd0-108">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02dd0-109">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="02dd0-109">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="02dd0-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="02dd0-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="02dd0-111">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-111">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02dd0-112">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-112">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02dd0-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02dd0-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02dd0-114">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="02dd0-114">Consumption patterns</span></span>

<span data-ttu-id="02dd0-115">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-115">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02dd0-116">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-116">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02dd0-117">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-117">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02dd0-118">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-118">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02dd0-119">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-119">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02dd0-120">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="02dd0-120">None of them are strictly superior to another.</span></span> <span data-ttu-id="02dd0-121">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-121">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02dd0-122">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-122">Basic usage</span></span>

<span data-ttu-id="02dd0-123">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-123">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="02dd0-124">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="02dd0-124">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02dd0-125">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02dd0-125">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02dd0-126">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-126">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="02dd0-127">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-127">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="02dd0-128">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-128">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02dd0-129">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-129">Named clients</span></span>

<span data-ttu-id="02dd0-130">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="02dd0-130">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="02dd0-131">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-131">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02dd0-132">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="02dd0-132">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="02dd0-133">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-133">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="02dd0-134">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-134">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="02dd0-135">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-135">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="02dd0-136">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="02dd0-136">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02dd0-137">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="02dd0-137">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02dd0-138">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-138">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02dd0-139">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-139">Typed clients</span></span>

<span data-ttu-id="02dd0-140">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-140">Typed clients:</span></span>

* <span data-ttu-id="02dd0-141">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="02dd0-141">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02dd0-142">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-142">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02dd0-143">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-143">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02dd0-144">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-144">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="02dd0-145">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-145">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="02dd0-146">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-146">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02dd0-147">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-147">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="02dd0-148">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="02dd0-148">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="02dd0-149">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-149">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02dd0-150">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-150">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="02dd0-151">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-151">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02dd0-152">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-152">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02dd0-153">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="02dd0-153">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02dd0-154">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-154">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02dd0-155">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-155">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="02dd0-156">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="02dd0-156">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02dd0-157">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="02dd0-157">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="02dd0-158">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-158">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02dd0-159">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-159">Generated clients</span></span>

<span data-ttu-id="02dd0-160">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02dd0-160">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02dd0-161">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-161">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02dd0-162">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-162">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02dd0-163">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-163">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02dd0-164">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="02dd0-164">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02dd0-165">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-165">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02dd0-166">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-166">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02dd0-167">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="02dd0-167">Outgoing request middleware</span></span>

<span data-ttu-id="02dd0-168">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-168">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02dd0-169">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-169">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="02dd0-170">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="02dd0-170">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="02dd0-171">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-171">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="02dd0-172">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02dd0-172">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="02dd0-173">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-173">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="02dd0-174">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-174">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="02dd0-175">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-175">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02dd0-176">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-176">The preceding code defines a basic handler.</span></span> <span data-ttu-id="02dd0-177">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-177">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="02dd0-178">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-178">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="02dd0-179">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="02dd0-179">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="02dd0-180">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-180">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="02dd0-181">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-181">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02dd0-182">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-182">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="02dd0-183">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="02dd0-183">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="02dd0-184">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-184">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="02dd0-185">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-185">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="02dd0-186">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-186">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02dd0-187">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="02dd0-187">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="02dd0-188">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="02dd0-188">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02dd0-189">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-189">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="02dd0-190">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-190">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="02dd0-191">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="02dd0-191">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02dd0-192">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-192">Use Polly-based handlers</span></span>

<span data-ttu-id="02dd0-193">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02dd0-193">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02dd0-194">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-194">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02dd0-195">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="02dd0-195">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02dd0-196">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-196">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-197">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="02dd0-197">The Polly extensions:</span></span>

* <span data-ttu-id="02dd0-198">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-198">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="02dd0-199">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-199">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="02dd0-200">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-200">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02dd0-201">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="02dd0-201">Handle transient faults</span></span>

<span data-ttu-id="02dd0-202">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="02dd0-202">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02dd0-203">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="02dd0-203">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02dd0-204">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="02dd0-204">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="02dd0-205">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-205">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02dd0-206">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-206">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="02dd0-207">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-207">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02dd0-208">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="02dd0-208">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02dd0-209">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="02dd0-209">Dynamically select policies</span></span>

<span data-ttu-id="02dd0-210">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-210">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="02dd0-211">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-211">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="02dd0-212">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-212">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02dd0-213">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-213">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02dd0-214">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-214">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02dd0-215">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-215">Add multiple Polly handlers</span></span>

<span data-ttu-id="02dd0-216">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="02dd0-216">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02dd0-217">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-217">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="02dd0-218">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-218">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="02dd0-219">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="02dd0-219">Failed requests are retried up to three times.</span></span> <span data-ttu-id="02dd0-220">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-220">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="02dd0-221">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="02dd0-221">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="02dd0-222">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="02dd0-222">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02dd0-223">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-223">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02dd0-224">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-224">Add policies from the Polly registry</span></span>

<span data-ttu-id="02dd0-225">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-225">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="02dd0-226">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-226">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="02dd0-227">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="02dd0-227">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="02dd0-228">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="02dd0-228">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="02dd0-229">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02dd0-229">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02dd0-230">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="02dd0-230">HttpClient and lifetime management</span></span>

<span data-ttu-id="02dd0-231">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-231">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-232">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-232">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="02dd0-233">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-233">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02dd0-234">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="02dd0-234">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02dd0-235">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="02dd0-235">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02dd0-236">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-236">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02dd0-237">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-237">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02dd0-238">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="02dd0-238">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="02dd0-239">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="02dd0-239">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02dd0-240">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-240">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="02dd0-241">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-241">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="02dd0-242">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-242">Disposal of the client isn't required.</span></span> <span data-ttu-id="02dd0-243">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-243">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02dd0-244">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-244">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-245">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-245">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="02dd0-246">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-246">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-247">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-247">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="02dd0-248">protokolování</span><span class="sxs-lookup"><span data-stu-id="02dd0-248">Logging</span></span>

<span data-ttu-id="02dd0-249">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-249">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02dd0-250">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-250">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="02dd0-251">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-251">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02dd0-252">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-252">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02dd0-253">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-253">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="02dd0-254">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-254">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-255">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-255">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02dd0-256">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-256">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02dd0-257">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-257">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-258">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-258">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="02dd0-259">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-259">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="02dd0-260">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-260">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02dd0-261">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-261">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02dd0-262">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-262">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="02dd0-263">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="02dd0-263">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02dd0-264">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02dd0-264">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02dd0-265">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="02dd0-265">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02dd0-266">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-266">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02dd0-267">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-267">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02dd0-268">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="02dd0-268">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02dd0-269">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="02dd0-269">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02dd0-270">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="02dd0-270">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02dd0-271">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="02dd0-271">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02dd0-272">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="02dd0-272">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02dd0-273">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-273">In the following example:</span></span>

* <span data-ttu-id="02dd0-274"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-274"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02dd0-275">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-275">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02dd0-276">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-276">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02dd0-277">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-277">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="02dd0-278">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02dd0-278">Additional resources</span></span>

* [<span data-ttu-id="02dd0-279">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="02dd0-279">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02dd0-280">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-280">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02dd0-281">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="02dd0-281">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="02dd0-282">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="02dd0-282">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="02dd0-283"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-283">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02dd0-284">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="02dd0-284">It offers the following benefits:</span></span>

* <span data-ttu-id="02dd0-285">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="02dd0-285">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-286">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02dd0-286">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02dd0-287">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="02dd0-287">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="02dd0-288">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="02dd0-288">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="02dd0-289">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-289">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02dd0-290">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-290">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02dd0-291">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02dd0-291">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02dd0-292">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="02dd0-292">Consumption patterns</span></span>

<span data-ttu-id="02dd0-293">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-293">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02dd0-294">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-294">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02dd0-295">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-295">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02dd0-296">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-296">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02dd0-297">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-297">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02dd0-298">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="02dd0-298">None of them are strictly superior to another.</span></span> <span data-ttu-id="02dd0-299">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-299">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02dd0-300">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-300">Basic usage</span></span>

<span data-ttu-id="02dd0-301">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-301">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="02dd0-302">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="02dd0-302">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02dd0-303">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02dd0-303">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02dd0-304">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-304">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="02dd0-305">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-305">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="02dd0-306">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-306">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02dd0-307">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-307">Named clients</span></span>

<span data-ttu-id="02dd0-308">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="02dd0-308">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="02dd0-309">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-309">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02dd0-310">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="02dd0-310">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="02dd0-311">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-311">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="02dd0-312">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-312">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="02dd0-313">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-313">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="02dd0-314">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="02dd0-314">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02dd0-315">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="02dd0-315">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02dd0-316">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-316">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02dd0-317">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-317">Typed clients</span></span>

<span data-ttu-id="02dd0-318">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-318">Typed clients:</span></span>

* <span data-ttu-id="02dd0-319">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="02dd0-319">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02dd0-320">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-320">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02dd0-321">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-321">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02dd0-322">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-322">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="02dd0-323">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-323">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="02dd0-324">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-324">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02dd0-325">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-325">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="02dd0-326">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="02dd0-326">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="02dd0-327">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-327">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02dd0-328">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-328">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="02dd0-329">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-329">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02dd0-330">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-330">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02dd0-331">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="02dd0-331">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02dd0-332">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-332">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02dd0-333">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-333">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="02dd0-334">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="02dd0-334">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02dd0-335">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="02dd0-335">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="02dd0-336">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-336">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02dd0-337">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-337">Generated clients</span></span>

<span data-ttu-id="02dd0-338">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02dd0-338">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02dd0-339">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-339">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02dd0-340">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-340">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02dd0-341">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-341">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02dd0-342">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="02dd0-342">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02dd0-343">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-343">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02dd0-344">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-344">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02dd0-345">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="02dd0-345">Outgoing request middleware</span></span>

<span data-ttu-id="02dd0-346">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-346">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02dd0-347">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-347">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="02dd0-348">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="02dd0-348">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="02dd0-349">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-349">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="02dd0-350">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02dd0-350">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="02dd0-351">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-351">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="02dd0-352">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-352">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="02dd0-353">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-353">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02dd0-354">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-354">The preceding code defines a basic handler.</span></span> <span data-ttu-id="02dd0-355">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-355">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="02dd0-356">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-356">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="02dd0-357">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="02dd0-357">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="02dd0-358">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-358">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="02dd0-359">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-359">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02dd0-360">`IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-360">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="02dd0-361">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="02dd0-361">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="02dd0-362">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-362">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="02dd0-363">Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-363">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="02dd0-364">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-364">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02dd0-365">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="02dd0-365">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="02dd0-366">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="02dd0-366">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02dd0-367">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-367">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="02dd0-368">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-368">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="02dd0-369">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="02dd0-369">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02dd0-370">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-370">Use Polly-based handlers</span></span>

<span data-ttu-id="02dd0-371">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02dd0-371">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02dd0-372">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-372">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02dd0-373">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="02dd0-373">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02dd0-374">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-374">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-375">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="02dd0-375">The Polly extensions:</span></span>

* <span data-ttu-id="02dd0-376">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-376">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="02dd0-377">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-377">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="02dd0-378">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-378">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02dd0-379">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="02dd0-379">Handle transient faults</span></span>

<span data-ttu-id="02dd0-380">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="02dd0-380">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02dd0-381">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="02dd0-381">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02dd0-382">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="02dd0-382">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="02dd0-383">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-383">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02dd0-384">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-384">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="02dd0-385">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-385">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02dd0-386">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="02dd0-386">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02dd0-387">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="02dd0-387">Dynamically select policies</span></span>

<span data-ttu-id="02dd0-388">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-388">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="02dd0-389">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-389">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="02dd0-390">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-390">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02dd0-391">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-391">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02dd0-392">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-392">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02dd0-393">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-393">Add multiple Polly handlers</span></span>

<span data-ttu-id="02dd0-394">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="02dd0-394">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02dd0-395">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-395">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="02dd0-396">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-396">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="02dd0-397">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="02dd0-397">Failed requests are retried up to three times.</span></span> <span data-ttu-id="02dd0-398">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-398">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="02dd0-399">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="02dd0-399">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="02dd0-400">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="02dd0-400">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02dd0-401">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-401">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02dd0-402">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-402">Add policies from the Polly registry</span></span>

<span data-ttu-id="02dd0-403">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-403">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="02dd0-404">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-404">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="02dd0-405">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="02dd0-405">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="02dd0-406">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="02dd0-406">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="02dd0-407">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02dd0-407">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02dd0-408">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="02dd0-408">HttpClient and lifetime management</span></span>

<span data-ttu-id="02dd0-409">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-409">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-410">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-410">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="02dd0-411">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-411">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02dd0-412">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="02dd0-412">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02dd0-413">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="02dd0-413">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02dd0-414">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-414">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02dd0-415">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-415">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02dd0-416">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="02dd0-416">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="02dd0-417">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="02dd0-417">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02dd0-418">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-418">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="02dd0-419">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-419">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="02dd0-420">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-420">Disposal of the client isn't required.</span></span> <span data-ttu-id="02dd0-421">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-421">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02dd0-422">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-422">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-423">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-423">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="02dd0-424">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-424">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-425">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-425">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="02dd0-426">protokolování</span><span class="sxs-lookup"><span data-stu-id="02dd0-426">Logging</span></span>

<span data-ttu-id="02dd0-427">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-427">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02dd0-428">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-428">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="02dd0-429">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-429">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02dd0-430">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-430">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02dd0-431">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-431">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="02dd0-432">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-432">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-433">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-433">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02dd0-434">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-434">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02dd0-435">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-435">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-436">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-436">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="02dd0-437">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-437">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="02dd0-438">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-438">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02dd0-439">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-439">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02dd0-440">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-440">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="02dd0-441">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="02dd0-441">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02dd0-442">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02dd0-442">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02dd0-443">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="02dd0-443">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02dd0-444">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-444">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02dd0-445">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-445">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02dd0-446">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="02dd0-446">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02dd0-447">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="02dd0-447">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02dd0-448">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="02dd0-448">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02dd0-449">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="02dd0-449">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02dd0-450">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="02dd0-450">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02dd0-451">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-451">In the following example:</span></span>

* <span data-ttu-id="02dd0-452"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-452"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02dd0-453">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-453">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02dd0-454">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-454">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02dd0-455">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-455">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="02dd0-456">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02dd0-456">Additional resources</span></span>

* [<span data-ttu-id="02dd0-457">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="02dd0-457">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02dd0-458">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-458">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02dd0-459">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="02dd0-459">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="02dd0-460">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="02dd0-460">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="02dd0-461"><xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-461">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="02dd0-462">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="02dd0-462">It offers the following benefits:</span></span>

* <span data-ttu-id="02dd0-463">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="02dd0-463">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-464">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="02dd0-464">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="02dd0-465">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="02dd0-465">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="02dd0-466">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="02dd0-466">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="02dd0-467">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-467">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="02dd0-468">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-468">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="02dd0-469">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02dd0-469">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="02dd0-470">Požadavky</span><span class="sxs-lookup"><span data-stu-id="02dd0-470">Prerequisites</span></span>

<span data-ttu-id="02dd0-471">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="02dd0-471">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="02dd0-472">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už obsahují balíček `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-472">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="02dd0-473">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="02dd0-473">Consumption patterns</span></span>

<span data-ttu-id="02dd0-474">Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-474">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="02dd0-475">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-475">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="02dd0-476">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-476">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="02dd0-477">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-477">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="02dd0-478">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-478">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="02dd0-479">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="02dd0-479">None of them are strictly superior to another.</span></span> <span data-ttu-id="02dd0-480">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-480">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="02dd0-481">Základní využití</span><span class="sxs-lookup"><span data-stu-id="02dd0-481">Basic usage</span></span>

<span data-ttu-id="02dd0-482">`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-482">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="02dd0-483">Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="02dd0-483">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="02dd0-484">`IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="02dd0-484">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="02dd0-485">Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-485">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="02dd0-486">Nemá žádný vliv na způsob, jakým se používá `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-486">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="02dd0-487">V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-487">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="02dd0-488">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-488">Named clients</span></span>

<span data-ttu-id="02dd0-489">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="02dd0-489">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="02dd0-490">Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-490">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="02dd0-491">V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="02dd0-491">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="02dd0-492">U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-492">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="02dd0-493">Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="02dd0-493">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="02dd0-494">Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-494">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="02dd0-495">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="02dd0-495">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="02dd0-496">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="02dd0-496">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="02dd0-497">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-497">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="02dd0-498">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="02dd0-498">Typed clients</span></span>

<span data-ttu-id="02dd0-499">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-499">Typed clients:</span></span>

* <span data-ttu-id="02dd0-500">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="02dd0-500">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="02dd0-501">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-501">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="02dd0-502">Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-502">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="02dd0-503">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-503">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="02dd0-504">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02dd0-504">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="02dd0-505">Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-505">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="02dd0-506">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-506">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="02dd0-507">Objekt `HttpClient` je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="02dd0-507">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="02dd0-508">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-508">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="02dd0-509">Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-509">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="02dd0-510">K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-510">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="02dd0-511">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-511">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="02dd0-512">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="02dd0-512">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="02dd0-513">Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-513">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="02dd0-514">Je možné zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-514">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="02dd0-515">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="02dd0-515">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="02dd0-516">V předchozím kódu je `HttpClient` uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="02dd0-516">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="02dd0-517">Veškerý přístup k externím voláním projde metodou `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-517">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="02dd0-518">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="02dd0-518">Generated clients</span></span>

<span data-ttu-id="02dd0-519">`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="02dd0-519">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="02dd0-520">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-520">Refit is a REST library for .NET.</span></span> <span data-ttu-id="02dd0-521">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-521">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="02dd0-522">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-522">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="02dd0-523">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="02dd0-523">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="02dd0-524">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-524">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="02dd0-525">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="02dd0-525">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="02dd0-526">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="02dd0-526">Outgoing request middleware</span></span>

<span data-ttu-id="02dd0-527">`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-527">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="02dd0-528">`IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-528">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="02dd0-529">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="02dd0-529">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="02dd0-530">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-530">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="02dd0-531">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02dd0-531">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="02dd0-532">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-532">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="02dd0-533">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-533">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="02dd0-534">Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-534">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="02dd0-535">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-535">The preceding code defines a basic handler.</span></span> <span data-ttu-id="02dd0-536">Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-536">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="02dd0-537">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-537">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="02dd0-538">Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="02dd0-538">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="02dd0-539">Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-539">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="02dd0-540">V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI.</span><span class="sxs-lookup"><span data-stu-id="02dd0-540">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="02dd0-541">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="02dd0-541">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="02dd0-542">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="02dd0-542">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="02dd0-543">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="02dd0-543">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="02dd0-544">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-544">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="02dd0-545">Po zaregistrování můžete <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> volat a předat do něj typ obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-545">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="02dd0-546">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-546">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="02dd0-547">Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="02dd0-547">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="02dd0-548">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="02dd0-548">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="02dd0-549">Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-549">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="02dd0-550">Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-550">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="02dd0-551">Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="02dd0-551">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="02dd0-552">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-552">Use Polly-based handlers</span></span>

<span data-ttu-id="02dd0-553">`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="02dd0-553">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="02dd0-554">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="02dd0-554">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="02dd0-555">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="02dd0-555">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="02dd0-556">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-556">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-557">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="02dd0-557">The Polly extensions:</span></span>

* <span data-ttu-id="02dd0-558">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-558">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="02dd0-559">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-559">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="02dd0-560">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="02dd0-560">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="02dd0-561">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="02dd0-561">Handle transient faults</span></span>

<span data-ttu-id="02dd0-562">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="02dd0-562">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="02dd0-563">K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="02dd0-563">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="02dd0-564">Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="02dd0-564">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="02dd0-565">Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-565">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02dd0-566">Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-566">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="02dd0-567">V předchozím kódu je definována zásada `WaitAndRetryAsync`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-567">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="02dd0-568">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="02dd0-568">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="02dd0-569">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="02dd0-569">Dynamically select policies</span></span>

<span data-ttu-id="02dd0-570">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-570">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="02dd0-571">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-571">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="02dd0-572">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="02dd0-572">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="02dd0-573">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-573">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="02dd0-574">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="02dd0-574">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="02dd0-575">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-575">Add multiple Polly handlers</span></span>

<span data-ttu-id="02dd0-576">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="02dd0-576">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="02dd0-577">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-577">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="02dd0-578">První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-578">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="02dd0-579">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="02dd0-579">Failed requests are retried up to three times.</span></span> <span data-ttu-id="02dd0-580">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-580">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="02dd0-581">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="02dd0-581">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="02dd0-582">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="02dd0-582">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="02dd0-583">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-583">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="02dd0-584">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-584">Add policies from the Polly registry</span></span>

<span data-ttu-id="02dd0-585">Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-585">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="02dd0-586">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="02dd0-586">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="02dd0-587">V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="02dd0-587">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="02dd0-588">Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="02dd0-588">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="02dd0-589">Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="02dd0-589">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="02dd0-590">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="02dd0-590">HttpClient and lifetime management</span></span>

<span data-ttu-id="02dd0-591">Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-591">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-592">Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-592">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="02dd0-593">Továrna spravuje životnost instancí `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-593">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="02dd0-594">`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="02dd0-594">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="02dd0-595">Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.</span><span class="sxs-lookup"><span data-stu-id="02dd0-595">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="02dd0-596">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="02dd0-596">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="02dd0-597">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-597">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="02dd0-598">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="02dd0-598">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="02dd0-599">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="02dd0-599">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="02dd0-600">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="02dd0-600">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="02dd0-601">Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="02dd0-601">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="02dd0-602">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-602">Disposal of the client isn't required.</span></span> <span data-ttu-id="02dd0-603">Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="02dd0-603">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="02dd0-604">`IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-604">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="02dd0-605">Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="02dd0-605">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="02dd0-606">Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-606">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="02dd0-607">Tento model se po migraci na `IHttpClientFactory`nepotřebuje.</span><span class="sxs-lookup"><span data-stu-id="02dd0-607">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="02dd0-608">protokolování</span><span class="sxs-lookup"><span data-stu-id="02dd0-608">Logging</span></span>

<span data-ttu-id="02dd0-609">Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-609">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="02dd0-610">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-610">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="02dd0-611">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="02dd0-611">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="02dd0-612">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-612">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="02dd0-613">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-613">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="02dd0-614">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-614">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-615">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-615">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="02dd0-616">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="02dd0-616">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="02dd0-617">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-617">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="02dd0-618">V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-618">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="02dd0-619">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="02dd0-619">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="02dd0-620">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="02dd0-620">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="02dd0-621">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="02dd0-621">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="02dd0-622">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="02dd0-622">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="02dd0-623">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="02dd0-623">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="02dd0-624">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="02dd0-624">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="02dd0-625">Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.</span><span class="sxs-lookup"><span data-stu-id="02dd0-625">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="02dd0-626">Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-626">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="02dd0-627">Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="02dd0-627">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="02dd0-628">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:</span><span class="sxs-lookup"><span data-stu-id="02dd0-628">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="02dd0-629">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="02dd0-629">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="02dd0-630">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="02dd0-630">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="02dd0-631">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="02dd0-631">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="02dd0-632">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="02dd0-632">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="02dd0-633">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="02dd0-633">In the following example:</span></span>

* <span data-ttu-id="02dd0-634"><xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="02dd0-634"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="02dd0-635">`MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="02dd0-635">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="02dd0-636">`HttpClient` slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="02dd0-636">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="02dd0-637">`Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="02dd0-637">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="02dd0-638">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02dd0-638">Additional resources</span></span>

* [<span data-ttu-id="02dd0-639">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="02dd0-639">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="02dd0-640">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="02dd0-640">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="02dd0-641">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="02dd0-641">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end