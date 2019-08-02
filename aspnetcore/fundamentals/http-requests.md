---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/01/2019
uid: fundamentals/http-requests
ms.openlocfilehash: bcf2a2eaf6910222d274c38bac343c92fab9cb5b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739539"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="e0a8a-103">Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0a8a-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

<span data-ttu-id="e0a8a-104">Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="e0a8a-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="e0a8a-105">Je možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci. <xref:System.Net.Http.IHttpClientFactory></span><span class="sxs-lookup"><span data-stu-id="e0a8a-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="e0a8a-106">Nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-106">It offers the following benefits:</span></span>

* <span data-ttu-id="e0a8a-107">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e0a8a-108">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-108">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="e0a8a-109">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-109">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e0a8a-110">Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin `HttpClient` v a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="e0a8a-111">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-111">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e0a8a-112">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-112">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e0a8a-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e0a8a-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="e0a8a-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e0a8a-114">Prerequisites</span></span>

<span data-ttu-id="e0a8a-115">Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-115">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="e0a8a-116">Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , `Microsoft.Extensions.Http` už balíček obsahuje.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-116">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

::: moniker-end

## <a name="consumption-patterns"></a><span data-ttu-id="e0a8a-117">Vzorce spotřeby</span><span class="sxs-lookup"><span data-stu-id="e0a8a-117">Consumption patterns</span></span>

<span data-ttu-id="e0a8a-118">V aplikaci lze použít `IHttpClientFactory` několik způsobů:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-118">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="e0a8a-119">Základní využití</span><span class="sxs-lookup"><span data-stu-id="e0a8a-119">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="e0a8a-120">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="e0a8a-120">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="e0a8a-121">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="e0a8a-121">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="e0a8a-122">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="e0a8a-122">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="e0a8a-123">Žádná z nich není výhradně nadřízena jiným.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-123">None of them are strictly superior to another.</span></span> <span data-ttu-id="e0a8a-124">Nejlepší přístup závisí na omezeních aplikace.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-124">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="e0a8a-125">Základní využití</span><span class="sxs-lookup"><span data-stu-id="e0a8a-125">Basic usage</span></span>

<span data-ttu-id="e0a8a-126">Lze zaregistrovat voláním metody `IServiceCollection` `AddHttpClient` rozšíření`Startup.ConfigureServices` v, uvnitř metody. `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-126">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="e0a8a-127">Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s vkládáním závislostí [(di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-127">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e0a8a-128">Lze použít k `HttpClient` vytvoření instance: `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-128">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="e0a8a-129">`IHttpClientFactory` Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-129">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="e0a8a-130">Nemá žádný vliv na způsob `HttpClient` použití.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-130">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="e0a8a-131">Na místech, `HttpClient` kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty <xref:System.Net.Http.IHttpClientFactory.CreateClient*>voláním.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-131">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="e0a8a-132">Pojmenovaná klienti</span><span class="sxs-lookup"><span data-stu-id="e0a8a-132">Named clients</span></span>

<span data-ttu-id="e0a8a-133">Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každý s jinou konfigurací, je možnost používat **pojmenované klienty**.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-133">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="e0a8a-134">Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-134">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="e0a8a-135">V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-135">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="e0a8a-136">U tohoto klienta se používá některá výchozí&mdash;konfigurace, konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-136">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="e0a8a-137">Pokaždé `CreateClient` , když se zavolá, vytvoří `HttpClient` se nová instance a pak se zavolá akce konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-137">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="e0a8a-138">Chcete-li využívat pojmenovaného klienta, lze předat `CreateClient`parametr řetězce.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-138">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="e0a8a-139">Zadejte název klienta, který se má vytvořit:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-139">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="e0a8a-140">V předchozím kódu nemusí požadavek určovat název hostitele.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-140">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="e0a8a-141">Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-141">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="e0a8a-142">Typové klienti</span><span class="sxs-lookup"><span data-stu-id="e0a8a-142">Typed clients</span></span>

<span data-ttu-id="e0a8a-143">Klienti typu:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-143">Typed clients:</span></span>

* <span data-ttu-id="e0a8a-144">Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-144">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="e0a8a-145">Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-145">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="e0a8a-146">Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s `HttpClient`ním.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-146">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="e0a8a-147">Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-147">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="e0a8a-148">Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-148">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="e0a8a-149">Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-149">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e0a8a-150">V předchozím kódu se konfigurace přesune do typovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-150">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="e0a8a-151">`HttpClient` Objekt je vystaven jako veřejná vlastnost.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-151">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="e0a8a-152">Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-152">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="e0a8a-153">`GetAspNetDocsIssues` Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-153">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="e0a8a-154">K registraci typovaného klienta lze použít obecnou <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> metodu rozšíření v rámci `Startup.ConfigureServices`a určení typové třídy klienta:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-154">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="e0a8a-155">Typový klient je zaregistrován jako přechodný s DI.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-155">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="e0a8a-156">Typového klienta lze vložit a spotřebovat přímo:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-156">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="e0a8a-157">Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během `Startup.ConfigureServices`registrace v nástroji, nikoli v konstruktoru typovaného klienta:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-157">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="e0a8a-158">Je možné úplně zapouzdřit `HttpClient` v rámci zadaného klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-158">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="e0a8a-159">Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které `HttpClient` volají instanci interně.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-159">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e0a8a-160">V předchozím kódu `HttpClient` je uložen jako soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-160">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="e0a8a-161">Veškerý přístup k externím voláním prochází `GetRepos` metodou.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-161">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="e0a8a-162">Vygenerované klienty</span><span class="sxs-lookup"><span data-stu-id="e0a8a-162">Generated clients</span></span>

<span data-ttu-id="e0a8a-163">`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-163">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="e0a8a-164">REFIT je knihovna REST pro .NET.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-164">Refit is a REST library for .NET.</span></span> <span data-ttu-id="e0a8a-165">Převede rozhraní REST API na živá rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-165">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="e0a8a-166">Implementace rozhraní je vygenerována dynamicky `RestService`pomocí, a používá `HttpClient` se k vytvoření externích volání http.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-166">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="e0a8a-167">Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-167">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="e0a8a-168">K vygenerování implementace se dá přidat typový klient s použitím REFIT:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-168">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="e0a8a-169">Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-169">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="e0a8a-170">Middleware odchozího požadavku</span><span class="sxs-lookup"><span data-stu-id="e0a8a-170">Outgoing request middleware</span></span>

<span data-ttu-id="e0a8a-171">`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-171">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="e0a8a-172">`IHttpClientFactory` Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-172">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="e0a8a-173">Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-173">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e0a8a-174">Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-174">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="e0a8a-175">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-175">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e0a8a-176">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-176">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="e0a8a-177">Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-177">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="e0a8a-178">Před předáním požadavku další obslužné rutině v kanálu přepište metoduprospuštěníkódu:`SendAsync`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-178">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="e0a8a-179">Předchozí kód definuje základní obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-179">The preceding code defines a basic handler.</span></span> <span data-ttu-id="e0a8a-180">Kontroluje, zda `X-API-KEY` byla do žádosti vložena hlavička.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-180">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="e0a8a-181">Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-181">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="e0a8a-182">Během registrace lze do konfigurace `HttpClient`nástroje Přidat jeden nebo více obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-182">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="e0a8a-183">Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-183">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e0a8a-184">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-184">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e0a8a-185">`IHttpClientFactory` Vytvoří samostatný obor di pro každou obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-185">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="e0a8a-186">Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-186">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="e0a8a-187">Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-187">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="e0a8a-188">Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> je možné ji volat s předáním typu pro obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-188">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e0a8a-189">V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-189">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="e0a8a-190">Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-190">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="e0a8a-191">Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-191">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="e0a8a-192">Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-192">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="e0a8a-193">Vyřazené obslužné služby způsobí selhání obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-193">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="e0a8a-194">Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> je možné je volat s předáním do typu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-194">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

::: moniker-end

<span data-ttu-id="e0a8a-195">Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-195">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="e0a8a-196">Každá obslužná rutina zabalí další obslužnou rutinu `HttpClientHandler` , dokud poslední nespustí požadavek:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-196">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="e0a8a-197">Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-197">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="e0a8a-198">Předejte data obslužné rutině `HttpRequestMessage.Properties`pomocí.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-198">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="e0a8a-199">Pro `IHttpContextAccessor` přístup k aktuálnímu požadavku použijte.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-199">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="e0a8a-200">Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-200">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="e0a8a-201">Použití obslužných rutin založené na Polly</span><span class="sxs-lookup"><span data-stu-id="e0a8a-201">Use Polly-based handlers</span></span>

<span data-ttu-id="e0a8a-202">`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-202">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="e0a8a-203">Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-203">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="e0a8a-204">Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-204">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="e0a8a-205">K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-205">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="e0a8a-206">Rozšíření Polly:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-206">The Polly extensions:</span></span>

* <span data-ttu-id="e0a8a-207">Podpora přidávání obslužných rutin na základě Polly do klientů.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-207">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="e0a8a-208">Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="e0a8a-208">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="e0a8a-209">Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-209">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="e0a8a-210">Zpracování přechodných chyb</span><span class="sxs-lookup"><span data-stu-id="e0a8a-210">Handle transient faults</span></span>

<span data-ttu-id="e0a8a-211">K většině běžných chyb dochází, když jsou externí volání HTTP přechodný.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-211">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="e0a8a-212">`AddTransientHttpErrorPolicy` Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-212">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="e0a8a-213">Zásady konfigurované pomocí této obslužné rutiny `HttpRequestException`metody rozšíření, odpovědí HTTP 5xx a odpovědí HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-213">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="e0a8a-214">Rozšíření lze použít v rámci `Startup.ConfigureServices`. `AddTransientHttpErrorPolicy`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-214">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e0a8a-215">Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-215">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="e0a8a-216">V předchozím kódu `WaitAndRetryAsync` je definována zásada.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-216">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="e0a8a-217">U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-217">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="e0a8a-218">Dynamické výběry zásad</span><span class="sxs-lookup"><span data-stu-id="e0a8a-218">Dynamically select policies</span></span>

<span data-ttu-id="e0a8a-219">Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-219">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="e0a8a-220">Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-220">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="e0a8a-221">Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-221">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="e0a8a-222">Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-222">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="e0a8a-223">Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-223">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="e0a8a-224">Přidání více obslužných rutin Polly</span><span class="sxs-lookup"><span data-stu-id="e0a8a-224">Add multiple Polly handlers</span></span>

<span data-ttu-id="e0a8a-225">Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-225">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="e0a8a-226">V předchozím příkladu jsou přidány dvě obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-226">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="e0a8a-227">První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-227">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="e0a8a-228">Neúspěšné požadavky se opakují třikrát.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-228">Failed requests are retried up to three times.</span></span> <span data-ttu-id="e0a8a-229">Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-229">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="e0a8a-230">Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-230">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="e0a8a-231">Zásady pro dělení na okruhy jsou stavové.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-231">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="e0a8a-232">Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-232">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="e0a8a-233">Přidání zásad z registru Polly</span><span class="sxs-lookup"><span data-stu-id="e0a8a-233">Add policies from the Polly registry</span></span>

<span data-ttu-id="e0a8a-234">Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-234">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="e0a8a-235">K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-235">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="e0a8a-236">V předchozím kódu jsou při `PolicyRegistry` přidání `ServiceCollection`do do. zaregistrovány dvě zásady.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-236">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="e0a8a-237">Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-237">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="e0a8a-238">Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="e0a8a-238">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="e0a8a-239">Správa HttpClient a životního cyklu</span><span class="sxs-lookup"><span data-stu-id="e0a8a-239">HttpClient and lifetime management</span></span>

<span data-ttu-id="e0a8a-240">Nová `HttpClient` instance se vrátí pokaždé, `CreateClient` když se zavolá `IHttpClientFactory`na.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-240">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="e0a8a-241"><xref:System.Net.Http.HttpMessageHandler> Existuje pro každého pojmenovaného klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-241">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="e0a8a-242">Továrna spravuje životnost `HttpMessageHandler` instancí.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-242">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="e0a8a-243">`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-243">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="e0a8a-244">Instance se dá znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela. `HttpMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-244">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="e0a8a-245">Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-245">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="e0a8a-246">Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-246">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="e0a8a-247">Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-247">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="e0a8a-248">Výchozí životnost obslužné rutiny je dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-248">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="e0a8a-249">Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-249">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="e0a8a-250">Chcete-li jej přepsat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> , zavolejte `IHttpClientBuilder` na, který je vrácen při vytváření klienta:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-250">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="e0a8a-251">Vyřazení klienta se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-251">Disposal of the client isn't required.</span></span> <span data-ttu-id="e0a8a-252">Vyřazení zruší odchozí žádosti a zaručuje, že `HttpClient` danou instanci nelze použít po volání <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-252">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="e0a8a-253">`IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-253">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="e0a8a-254">Instance `HttpClient` je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-254">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="e0a8a-255">Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-255">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="e0a8a-256">Tento model se po migraci na `IHttpClientFactory`nedá zbytečné.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-256">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="e0a8a-257">Protokolování</span><span class="sxs-lookup"><span data-stu-id="e0a8a-257">Logging</span></span>

<span data-ttu-id="e0a8a-258">Klienti vytvoření `IHttpClientFactory` prostřednictvím záznamu zprávy protokolu pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-258">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="e0a8a-259">V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-259">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="e0a8a-260">Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-260">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="e0a8a-261">Kategorie protokolu použitá pro každého klienta zahrnuje název klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-261">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="e0a8a-262">Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-262">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="e0a8a-263">Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-263">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="e0a8a-264">V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-264">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="e0a8a-265">Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-265">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="e0a8a-266">K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-266">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="e0a8a-267">V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`protokolu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-267">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="e0a8a-268">Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-268">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="e0a8a-269">V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-269">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="e0a8a-270">Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-270">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="e0a8a-271">To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-271">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="e0a8a-272">Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-272">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="e0a8a-273">Konfigurace HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="e0a8a-273">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="e0a8a-274">Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-274">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="e0a8a-275">Při přidávání pojmenovaných nebo typových klientů sevrátí.`IHttpClientBuilder`</span><span class="sxs-lookup"><span data-stu-id="e0a8a-275">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="e0a8a-276">Metodu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze použít k definování delegáta.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-276">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="e0a8a-277">Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-277">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="e0a8a-278">Použití IHttpClientFactory v konzolové aplikaci</span><span class="sxs-lookup"><span data-stu-id="e0a8a-278">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="e0a8a-279">V aplikaci konzoly přidejte do projektu následující odkazy na balíček:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-279">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="e0a8a-280">Microsoft. Extensions. hosting</span><span class="sxs-lookup"><span data-stu-id="e0a8a-280">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="e0a8a-281">Microsoft. Extensions. http</span><span class="sxs-lookup"><span data-stu-id="e0a8a-281">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="e0a8a-282">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e0a8a-282">In the following example:</span></span>

* <span data-ttu-id="e0a8a-283"><xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .</span><span class="sxs-lookup"><span data-stu-id="e0a8a-283"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="e0a8a-284">`MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-284">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="e0a8a-285">`HttpClient`slouží k načtení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-285">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="e0a8a-286">`Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.</span><span class="sxs-lookup"><span data-stu-id="e0a8a-286">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e0a8a-287">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e0a8a-287">Additional resources</span></span>

* [<span data-ttu-id="e0a8a-288">Použití HttpClientFactory k implementaci odolných požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="e0a8a-288">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="e0a8a-289">Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly</span><span class="sxs-lookup"><span data-stu-id="e0a8a-289">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="e0a8a-290">Implementace systému jističe</span><span class="sxs-lookup"><span data-stu-id="e0a8a-290">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
