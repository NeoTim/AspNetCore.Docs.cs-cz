---
title: Požadavky HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Další informace o použití rozhraní IHttpClientFactory ke správě instance logického HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 8b95f63c0e06a2b7d1d66064def192f91b8ffbb4
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874961"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Požadavky HTTP pomocí IHttpClientFactory v ASP.NET Core

Podle [Glenn Condron](https://github.com/glennc), [Ryanem Nowak](https://github.com/rynowak), a [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> Můžete zaregistrované a slouží ke konfiguraci a vytvořte <xref:System.Net.Http.HttpClient> instance v aplikaci. Nabízí následující výhody:

* Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Například *githubu* klient může zaregistrované a nakonfigurované pro přístup k [Githubu](https://github.com/). Výchozí klient může být zaregistrován k jiným účelům.
* Kodifikuje koncept odchozí middleware prostřednictvím delegování obslužné rutiny ve `HttpClient` a rozšíření pro middleware založený na Polly výhod, které poskytuje.
* Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.
* Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([stažení](xref:index#how-to-download-a-sample))

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a>Požadavky

Projekty cílené na rozhraní .NET Framework vyžadují instalaci [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) balíček NuGet. Projekty, které cílí na .NET Core a odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) již patří `Microsoft.Extensions.Http` balíčku.

::: moniker-end

## <a name="consumption-patterns"></a>Vzory využití

Existuje několik způsobů `IHttpClientFactory` lze použít v aplikaci:

* [Základní informace o využití](#basic-usage)
* [Pojmenované klientů](#named-clients)
* [Typy klientů](#typed-clients)
* [Vygenerovaný klientů](#generated-clients)

Žádná z nich není striktně vynikající do jiného. Nejlepší přístup, závisí na omezení aplikace.

### <a name="basic-usage"></a>Základní informace o využití

`IHttpClientFactory` Lze registrovat pomocí volání `AddHttpClient` rozšiřující metody na `IServiceCollection`uvnitř `Startup.ConfigureServices` metoda.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může přijmout kódu `IHttpClientFactory` kdekoli služby může být vložený se [injektáž závislostí (DI)](xref:fundamentals/dependency-injection). `IHttpClientFactory` Slouží k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Pomocí `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak Refaktorovat stávající aplikace. Nemá žádný vliv na způsob, jakým `HttpClient` se používá. Na místech, kde `HttpClient` aktuálně se vytvářejí instance, nahraďte volání výskyty <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Pojmenované klientů

Pokud aplikace vyžaduje použití mnoha různých `HttpClient`, každý s jinou konfiguraci, je možnost použití **s názvem klienti**. Konfigurace pro pojmenovaná `HttpClient` se dá nastavit během registrace v `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` je volána, že zadáte název *githubu*. Některé výchozí konfigurace má tento klient&mdash;totiž základní adrese a dvě záhlaví vyžadována pro práci s rozhraním API pro GitHub.

Pokaždé, když `CreateClient` nazývá novou instanci třídy `HttpClient` vytvoření a konfigurace akce je volána.

Využívat pojmenované klienta, může být předán parametr řetězce `CreateClient`. Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu žádost nemusí zadejte název hostitele. Ho můžete předat právě cestu, protože se používá základní adresu nakonfigurovanou pro klienta.

### <a name="typed-clients"></a>Typy klientů

Typy klientů poskytují stejné funkce jako pojmenované klientů bez nutnosti použití řetězců jako klíče. Typový klient přístup poskytuje IntelliSense a kompilátoru pomoct při využívání klientů. Poskytuje jedno centrální umístění pro konfiguraci a interakci s konkrétní `HttpClient`. Například konkrétního typu klienta může být použit pro koncový bod jediného back-endu a zapouzdření všech řešení logiku tohoto koncového bodu. Další výhodou je, že práce s DI a můžete vloženy vyžadováno ve vaší aplikaci.

Typový klient přijme `HttpClient` parametr v konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu konfigurace přesunout do typový klient. `HttpClient` Objektu je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které vystavují `HttpClient` funkce. `GetAspNetDocsIssues` Metoda zapouzdřuje kód potřebný k vyhledání a parsování nejnovější otevřené problémy z úložiště GitHub.

K registraci typový klient Obecné <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> metody rozšíření lze použít v `Startup.ConfigureServices`, určení typový klient třídy:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrovaný jako přechodné s DI. Typový klient může vloží a využívat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Pokud tomu dávají přednost, konfigurace pro typový klient se dá nastavit během registrace v `Startup.ConfigureServices`, spíše než v konstruktoru typu klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné zcela zapouzdření `HttpClient` v rámci typu klienta. Místo bude vystavená jako vlastnost, je možné veřejné metody poskytnou která volá `HttpClient` instance interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` se ukládá jako soukromé pole. Veškerý přístup pro volání externích prochází `GetRepos` metody.

### <a name="generated-clients"></a>Vygenerovaný klientů

`IHttpClientFactory` můžete použít v kombinaci s dalšími knihovnami třetích stran, jako [provedení nového](https://github.com/paulcbetts/refit). Refit je REST knihovna pro .NET. Rozhraní REST API se převede na živé interfaces. Implementace rozhraní se vygeneruje dynamicky pomocí `RestService`pomocí `HttpClient` aby externí HTTP volání.

Rozhraní a odpovědi jsou definovány pro reprezentaci externí rozhraní API a odpověď:

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

Typový klient můžete přidat, pomocí Refit generovat implementace:

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

Definované rozhraní můžete využívat v případě potřeby s implementací poskytované DI a Refit:

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

## <a name="outgoing-request-middleware"></a>Odchozí žádosti o middlewaru

`HttpClient` již obsahuje koncept delegování obslužné rutiny, které může být propojený pro odchozí požadavky HTTP. `IHttpClientFactory` Usnadňuje k definování obslužných rutin mají použít u každého klienta s názvem. Podporuje registraci a zřetězení více obslužných rutin k sestavení kanál middleware odchozí požadavek. Každá z těchto obslužných rutin je moci provádět úkoly před a za odchozí požadavek. Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core. Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>. Přepsat `SendAsync` metoda spuštění kódu před předáním požadavku další obslužná rutina kanálu:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předcházející kód definuje obslužnou rutinu základní. Zkontroluje, jestli `X-API-KEY` záhlaví byla zahrnuta v požadavku. Pokud chybí záhlaví, můžete vyhnout volání HTTP a vrátí odpověď vhodný.

Během registrace, lze přidat jeden nebo více obslužných rutin do konfigurace `HttpClient`. Tato úloha se provádí prostřednictvím metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

V předchozím kódu `ValidateHeaderHandler` DI zaregistrován. `IHttpClientFactory` Vytvoří samostatný obor DI pro každou obslužnou rutinu. Obslužné rutiny jsou zdarma záviset na služby žádný rozsah. Služby, které obslužné rutiny závisí na jsou uvolněno při uvolnění má obslužné rutiny.

Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> může být volána, předejte typ pro obslužné rutiny.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

V předchozím kódu `ValidateHeaderHandler` DI zaregistrován. Obslužná rutina **musí** zaregistrovat v DI jako přechodné služby, nikdy obor. Pokud obslužná rutina je zaregistrovaný jako vymezené služby a všech služeb, které závisí na obslužnou rutinu jsou na jedno použití, může být obslužné rutiny služby uvolněn předtím, než obslužná rutina dostane mimo rozsah, což by způsobilo obslužná rutina selže.

Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> může být volána, předejte typ obslužné rutiny.

::: moniker-end

Může být registrováno více obslužných rutin v pořadí, ve kterém by se měl spustit. Každý popisovač zabalí další obslužná rutina až do konečné `HttpClientHandler` zpracuje požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Použijte jednu z následujících dvou přístupů sdílet stav jednotlivých žádostí s obslužné rutiny zpráv:

* Předat data do obslužné rutiny pomocí `HttpRequestMessage.Properties`.
* Použití `IHttpContextAccessor` pro přístup k aktuální požadavek.
* Vytvoření vlastní `AsyncLocal` objekt úložiště předá data.

## <a name="use-polly-based-handlers"></a>Použít na základě Polly obslužné rutiny

`IHttpClientFactory` se integruje s oblíbenými knihovnu třetí strany s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnosti a přechodné zpracování chyb library pro .NET. Umožňuje vývojářům vyjádřit zásady například opakování, jističe, vypršení časového limitu, přepážka izolace a záložních fluent a bezpečným způsobem.

Metody rozšíření jsou k dispozici pro povolení použití zásad Polly nakonfigurované `HttpClient` instancí. Polly rozšíření:

* Podpora přidání obslužné rutiny na základě Polly klientům.
* Je možné po instalaci [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) balíček NuGet. Balíček není součástí sdíleného rozhraní ASP.NET Core.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

Většina běžných chyb nastane, když přechodné externí volání HTTP. Pohodlné rozšíření metoda volána `AddTransientHttpErrorPolicy` je zahrnuté umožňuje zásadu, která definované pro zpracování přechodných chyb. Zásady konfigurované pomocí tohoto úchytu – metoda rozšíření `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.

`AddTransientHttpErrorPolicy` Rozšíření může být použito v rámci `Startup.ConfigureServices`. Toto rozšíření poskytuje přístup k `PolicyBuilder` objekt nakonfigurovaný pro zpracování chyb představující možných přechodných chyb:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu `WaitAndRetryAsync` definované zásady. Neúspěšné požadavky se zopakují až třikrát s zpožděním mezi pokusy o 600 ms.

### <a name="dynamically-select-policies"></a>Dynamicky vybrat zásady

Další rozšiřující metody existují, které lze přidat na základě Polly obslužné rutiny. Je jedno takové rozšíření `AddPolicyHandler`, který má několik přetížení. Jedním přetížením mu umožní ho možné zkontrolovat při definování zásad, které chcete použít:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

V předchozím kódu pokud je odchozí požadavek HTTP GET, časový limit 10 sekundu se použije. Pro jiné metody HTTP se používá s časovým limitem 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidávání více obslužných rutin Polly

Je běžné vnořit Polly zásady, které poskytují vylepšené funkce:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První použití `AddTransientHttpErrorPolicy` rozšíření přidat zásady opakování. Neúspěšné požadavky se zopakují až třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu jističe. Další externí požadavky jsou blokovány 30 sekund, pokud postupně dojde k 5 neúspěšných pokusů o přihlášení. Jistič zásady jsou stavová. Všechna volání prostřednictvím tohoto klienta sdílet stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup pro správu pravidelně použité zásady, je jejich jednou definovat a registrovat pomocí `PolicyRegistry`. Metody rozšíření je k dispozici tomu, aby obslužná rutina přidat pomocí zásad z registru:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou registrovány dvě zásady, které při `PolicyRegistry` se přidá do `ServiceCollection`. Pomocí zásad z registru, `AddPolicyHandlerFromRegistry` metoda se používá, předejte název zásady začaly platit.

Další informace o `IHttpClientFactory` a integrace Polly můžete najít na [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient a životního cyklu správy

Nový `HttpClient` pokaždé, když je vrácena instance `CreateClient` je volán na `IHttpClientFactory`. Je <xref:System.Net.Http.HttpMessageHandler> jednoho klienta s názvem. Objekt pro vytváření spravuje životní cyklus `HttpMessageHandler` instancí.

`IHttpClientFactory` fondy `HttpMessageHandler` instancí, které jsou vytvořeny procesem ke snížení spotřeby prostředků. `HttpMessageHandler` Instance může být znovu použít z fondu při vytváření nového `HttpClient` instance Pokud nevypršela platnost svého životního cyklu.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje svou vlastní základní připojení protokolu HTTP. Vytváření více obslužných rutin, než je nezbytné, může způsobit zpoždění připojení. Některé obslužné rutiny také zachovat připojení otevřené po neomezenou dobu, což může zabránit obslužnou rutinu reakce na změny DNS.

Výchozí doba života obslužná rutina je dvě minuty. Výchozí hodnota se dá přepsat na základě pojmenované klienta. Chcete-li přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta není povinné. Vyřazení zruší odchozí požadavky a záruky daném `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` sleduje a uvolní prostředky využívané třídou `HttpClient` instancí. `HttpClient` Instancí lze obecně zacházet jako objekty .NET nevyžaduje vyřazení.

Zachování jediného `HttpClient` instance zachování připojení pro dlouhé době se běžně používá dříve, než vzniku `IHttpClientFactory`. Tento model se stane zbytečné po migraci na `IHttpClientFactory`.

## <a name="logging"></a>Protokolování

Klienty vytvořené prostřednictvím `IHttpClientFactory` záznam protokolu zpráv pro všechny požadavky. Povolte úroveň příslušné informace v konfiguraci protokolování tak, aby se zobrazit výchozí zprávy protokolu. Dodatečné protokolování, jako je například protokolování hlavičky požadavku je zahrnuta pouze na úroveň trasování.

Kategorie protokolu používá pro každého klienta obsahuje název klienta. Klient s názvem *MyNamedClient*, například zprávy protokolu s kategorii `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Zprávy doplněny *LogicalHandler* dochází mimo kanál obslužná rutina požadavku. V požadavku jsou zprávy zaznamenány předtím, než ji zpracovali ostatních obslužných rutin v kanálu. V odpovědi jsou zprávy zaznamenány po všech ostatních obslužných rutin kanálu mají přijetí odpovědi.

Také dochází k protokolování v kanálu obslužné rutiny žádosti. V *MyNamedClient* například tyto zprávy jsou zaznamenané v souvislosti s kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Pro žádost k tomu dochází po spuštění ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi protokolování zahrnuje stav odpovědi před odesláním zpět prostřednictvím kanálu obslužné rutiny.

Povolení protokolování mimo a v kanálu umožňuje kontrolu změny provedené ostatních obslužných rutin v kanálu. To může zahrnovat změny hlavičky, požadavku, například nebo stavový kód odpovědi.

Včetně názvu klienta v kategorii protokolů umožňuje filtrování konkrétních klientů s názvem v případě potřeby protokolování.

## <a name="configure-the-httpmessagehandler"></a>Objekt HttpMessageHandler konfigurace

Může být nutné k řízení konfigurace vnitřního `HttpMessageHandler` používaný klientem.

`IHttpClientBuilder` Dochází při přidávání s názvem nebo typy klientů. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Metody rozšíření lze použít k definování delegáta. Delegát se používá k vytvoření a konfigurace primární `HttpMessageHandler` používané tohoto klienta:

[!code-csharp[Main](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="additional-resources"></a>Další zdroje

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakování volání HTTP pomocí exponenciálního omezení rychlosti zásadám HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
