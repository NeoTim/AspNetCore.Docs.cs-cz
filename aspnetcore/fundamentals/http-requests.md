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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core

Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)

Je možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci. <xref:System.Net.Http.IHttpClientFactory> Nabízí následující výhody:

* Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/). Výchozí klient může být zaregistrován k jiným účelům.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin `HttpClient` v a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.
* Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.
* Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([stažení](xref:index#how-to-download-a-sample))

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a>Požadavky

Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet. Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , `Microsoft.Extensions.Http` už balíček obsahuje.

::: moniker-end

## <a name="consumption-patterns"></a>Vzorce spotřeby

V aplikaci lze použít `IHttpClientFactory` několik způsobů:

* [Základní využití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádná z nich není výhradně nadřízena jiným. Nejlepší přístup závisí na omezeních aplikace.

### <a name="basic-usage"></a>Základní využití

Lze zaregistrovat voláním metody `IServiceCollection` `AddHttpClient` rozšíření`Startup.ConfigureServices` v, uvnitř metody. `IHttpClientFactory`

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s vkládáním závislostí [(di)](xref:fundamentals/dependency-injection). Lze použít k `HttpClient` vytvoření instance: `IHttpClientFactory`

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory` Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci. Nemá žádný vliv na způsob `HttpClient` použití. Na místech, `HttpClient` kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty <xref:System.Net.Http.IHttpClientFactory.CreateClient*>voláním.

### <a name="named-clients"></a>Pojmenovaná klienti

Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každý s jinou konfigurací, je možnost používat **pojmenované klienty**. Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*. U tohoto klienta se používá některá výchozí&mdash;konfigurace, konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.

Pokaždé `CreateClient` , když se zavolá, vytvoří `HttpClient` se nová instance a pak se zavolá akce konfigurace.

Chcete-li využívat pojmenovaného klienta, lze předat `CreateClient`parametr řetězce. Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s `HttpClient`ním. Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.
* Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.

Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu se konfigurace přesune do typovaného klienta. `HttpClient` Objekt je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost. `GetAspNetDocsIssues` Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.

K registraci typovaného klienta lze použít obecnou <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> metodu rozšíření v rámci `Startup.ConfigureServices`a určení typové třídy klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během `Startup.ConfigureServices`registrace v nástroji, nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné úplně zapouzdřit `HttpClient` v rámci zadaného klienta. Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které `HttpClient` volají instanci interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen jako soukromé pole. Veškerý přístup k externím voláním prochází `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory`dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky `RestService`pomocí, a používá `HttpClient` se k vytvoření externích volání http.

Rozhraní a odpověď jsou definované tak, aby představovaly externí rozhraní API a jeho odpověď:

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

K vygenerování implementace se dá přidat typový klient s použitím REFIT:

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

Definované rozhraní lze v případě potřeby spotřebovat pomocí implementace, kterou poskytuje DI a REFIT:

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

## <a name="outgoing-request-middleware"></a>Middleware odchozího požadavku

`HttpClient`již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory` Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta. Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core. Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>. Před předáním požadavku další obslužné rutině v kanálu přepište metoduprospuštěníkódu:`SendAsync`

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Kontroluje, zda `X-API-KEY` byla do žádosti vložena hlavička. Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.

Během registrace lze do konfigurace `HttpClient`nástroje Přidat jeden nebo více obslužných rutin. Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di. `IHttpClientFactory` Vytvoří samostatný obor di pro každou obslužnou rutinu. Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru. Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.

Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> je možné ji volat s předáním typu pro obslužnou rutinu.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di. Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru. Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:

* Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.
* Vyřazené obslužné služby způsobí selhání obslužné rutiny.

Po registraci <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> je možné je volat s předáním do typu obslužné rutiny.

::: moniker-end

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu `HttpClientHandler` , dokud poslední nespustí požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data obslužné rutině `HttpRequestMessage.Properties`pomocí.
* Pro `IHttpContextAccessor` přístup k aktuálnímu požadavku použijte.
* Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory`integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi. Rozšíření Polly:

* Podpora přidávání obslužných rutin na základě Polly do klientů.
* Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K většině běžných chyb dochází, když jsou externí volání HTTP přechodný. `AddTransientHttpErrorPolicy` Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady konfigurované pomocí této obslužné rutiny `HttpRequestException`metody rozšíření, odpovědí HTTP 5xx a odpovědí HTTP 408.

Rozšíření lze použít v rámci `Startup.ConfigureServices`. `AddTransientHttpErrorPolicy` Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu `WaitAndRetryAsync` je definována zásada. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly. Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení. Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry`. K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou při `PolicyRegistry` přidání `ServiceCollection`do do. zaregistrovány dvě zásady. Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.

Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová `HttpClient` instance se vrátí pokaždé, `CreateClient` když se zavolá `IHttpClientFactory`na. <xref:System.Net.Http.HttpMessageHandler> Existuje pro každého pojmenovaného klienta. Továrna spravuje životnost `HttpMessageHandler` instancí.

`IHttpClientFactory`Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků. Instance se dá znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela. `HttpMessageHandler`

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů. Chcete-li jej přepsat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> , zavolejte `IHttpClientBuilder` na, který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta se nevyžaduje. Vyřazení zruší odchozí žádosti a zaručuje, že `HttpClient` danou instanci nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory`sleduje a odstraňuje prostředky používané `HttpClient` instancemi. Instance `HttpClient` je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.

Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`. Tento model se po migraci na `IHttpClientFactory`nedá zbytečné.

## <a name="logging"></a>Protokolování

Klienti vytvoření `IHttpClientFactory` prostřednictvím záznamu zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`protokolu. Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.

Při přidávání pojmenovaných nebo typových klientů sevrátí.`IHttpClientBuilder` Metodu <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory>je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService`Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`. `HttpClient`slouží k načtení webové stránky.
* `Main`vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
