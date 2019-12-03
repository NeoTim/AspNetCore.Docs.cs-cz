---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: f33444b8fc08dc022da7700af53a218600290162
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74733918"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin

<xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci. `IHttpClientFactory` nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Například klient s názvem *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/). Pro obecný přístup je možné zaregistrovat výchozího klienta.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient`. Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient`.
* Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`. Automatická správa zabraňuje běžným problémům DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient`ch dob života.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

Vzorový kód v této verzi tématu používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích HTTP. V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>`použijte selektor verzí k výběru verze 2. x tohoto tématu.

## <a name="consumption-patterns"></a>Vzorce spotřeby

Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:

* [Základní využití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Nejlepší přístup závisí na požadavcích aplikace.

### <a name="basic-usage"></a>Základní využití

`IHttpClientFactory` lze zaregistrovat voláním `AddHttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

`IHttpClientFactory` lze požadovat pomocí [Injektáže závislostí (di)](xref:fundamentals/dependency-injection). Následující kód používá `IHttpClientFactory` k vytvoření instance `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` jako v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci. Nemá žádný vliv na to, jak se používá `HttpClient`. V místech, kde jsou `HttpClient` instance vytvořené v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Pojmenovaná klienti

Pojmenovaná klienti jsou dobrou volbou v těchto případech:

* Aplikace vyžaduje mnoho různých použití `HttpClient`.
* Mnoho `HttpClient`s má odlišnou konfiguraci.

Konfigurace pro pojmenovanou `HttpClient` se dá zadat během registrace v `Startup.ConfigureServices`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu je klient nakonfigurován pomocí nástroje:

* Základní adresa `https://api.github.com/`.
* Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.

#### <a name="createclient"></a>CreateClient

Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*>, je volána:

* Vytvoří se nová instance `HttpClient`.
* Je volána akce konfigurace.

Chcete-li vytvořit pojmenovaného klienta, předejte jeho název do `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`. Můžete například použít jednoho typu klienta:
  * Pro jeden koncový bod back-endu.
  * Zapouzdřit veškerou práci s koncovým bodem.
* Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.

Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu:

* Konfigurace je přesunuta do typovaného klienta.
* Objekt `HttpClient` je vystaven jako veřejná vlastnost.

Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`. Například metoda `GetAspNetDocsIssues` zapouzdřuje kód, aby načetla otevřené problémy.

Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Konfigurace pro zadaného klienta se dá zadat během registrace v `Startup.ConfigureServices`místo v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient` lze zapouzdřit v rámci zadaného klienta. Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která interně volá instanci `HttpClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu je `HttpClient` uložen v soukromém poli. Přístup k `HttpClient` je veřejným `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` lze použít v kombinaci s knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.

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

    services.AddControllers();
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

`HttpClient` má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory`:

* Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.
* Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model:

  * Se podobá příchozímu kanálu middleware v ASP.NET Core.
  * Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:

    * ukládání do mezipaměti
    * zpracování chyb
    * serializace
    * protokolování

Vytvoření obslužné rutiny delegování:

* Odvodit z <xref:System.Net.Http.DelegatingHandler>.
* Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Před předáním požadavku další obslužné rutině v kanálu spusťte kód:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód zkontroluje, zda je hlavička `X-API-KEY` v žádosti. Pokud chybí `X-API-KEY`, vrátí se <xref:System.Net.HttpStatusCode.BadRequest>.

Do konfigurace pro `HttpClient` se dá přidat víc než jedna obslužná rutina s <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI. `IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu. Obslužné rutiny mohou záviset na službách jakéhokoli oboru. Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.

Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Pro přístup k aktuálnímu požadavku použijte <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>.
* Vytvořte vlastní objekt úložiště <xref:System.Threading.AsyncLocal`1>, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` se integruje s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany. Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`. Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů. Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K chybám obvykle dochází, když jsou externí volání HTTP přechodný. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady nakonfigurované pomocí `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:

* <xref:System.Net.Http.HttpRequestException>
* 5xx HTTP
* HTTP 408

`AddTransientHttpErrorPolicy` poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

V předchozím kódu je definována zásada `WaitAndRetryAsync`. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>. Následující `AddPolicyHandler` Overload zkontroluje požadavek na rozhodnutí, které zásady se mají použít:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu:

* Přidávají se dvě obslužné rutiny.
* První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát.
* Druhý `AddTransientHttpErrorPolicy` volání přidá zásadu dělení na okruhy. Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`.

V následujícím kódu:

* Přidávají se zásady "regular" a "Long".
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> přidá do registru zásady "regular" a "Long".

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Vytvoří se <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta. Továrna spravuje životnost instancí `HttpMessageHandler`.

`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků. Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána podle pojmenovaných klientů:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

instance `HttpClient` můžou být obecně ošetřené **jako objekty .NET, které** nevyžadují vyřazení. Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.

Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`. Tento model se po migraci na `IHttpClientFactory`nepotřebuje.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:

* Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.

- Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.

Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.

- `SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.

### <a name="cookies"></a>Soubory cookie

Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`. Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód. U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:

 - Zakázání automatického zpracování souborů cookie
 - Předcházení `IHttpClientFactory`

Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>protokolování

Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler". Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler". Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.

Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`. Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`. `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/). Výchozí klient může být zaregistrován pro jiné účely.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.
* Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Vzorce spotřeby

Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:

* [Základní využití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádná z nich není výhradně nadřízena jiným. Nejlepší přístup závisí na omezeních aplikace.

### <a name="basic-usage"></a>Základní využití

`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci. Nemá žádný vliv na způsob, jakým se používá `HttpClient`. V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Pojmenovaná klienti

Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**. Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*. U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.

Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.

Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`. Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`. Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.
* Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.

Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu se konfigurace přesune do typovaného klienta. Objekt `HttpClient` je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`. Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.

K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné zapouzdřit `HttpClient` v rámci zadaného klienta. Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu je `HttpClient` uložen jako soukromé pole. Veškerý přístup k externím voláním projde metodou `GetRepos`.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.

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
        c.BaseAddress = new Uri("https://localhost:5001");
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

`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta. Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>. Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`. Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.

Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin. Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI. `IHttpClientFactory` vytvoří samostatný obor DI pro každou obslužnou rutinu. Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru. Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.

Po zaregistrování je možné volat <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>, předání v typu pro obslužnou rutinu.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.
* Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.
* Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`. Rozšíření Polly:

* Podpora přidávání obslužných rutin na základě Polly do klientů.
* Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K většině běžných chyb dochází, když jsou externí volání HTTP přechodný. K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.

Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`. Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu je definována zásada `WaitAndRetryAsync`. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly. Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení. Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`. K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady. Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.

Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta. Továrna spravuje životnost instancí `HttpMessageHandler`.

`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků. Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů. Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta se nevyžaduje. Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi. Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.

Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`. Tento model se po migraci na `IHttpClientFactory`nepotřebuje.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:

* Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.

- Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.

Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.

- `SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.

### <a name="cookies"></a>Soubory cookie

Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`. Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód. U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:

 - Zakázání automatického zpracování souborů cookie
 - Předcházení `IHttpClientFactory`

Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>protokolování

Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.

Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`. Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`. `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> lze zaregistrovat a použít ke konfiguraci a vytvoření <xref:System.Net.Http.HttpClient> instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/). Výchozí klient může být zaregistrován pro jiné účely.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.
* Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet. Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už obsahují balíček `Microsoft.Extensions.Http`.

## <a name="consumption-patterns"></a>Vzorce spotřeby

Existuje několik způsobů, jak `IHttpClientFactory` lze v aplikaci použít:

* [Základní využití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádná z nich není výhradně nadřízena jiným. Nejlepší přístup závisí na omezeních aplikace.

### <a name="basic-usage"></a>Základní využití

`IHttpClientFactory` lze zaregistrovat voláním metody rozšíření `AddHttpClient` na `IServiceCollection`v rámci metody `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může kód přijmout `IHttpClientFactory` služby kdekoli, kde se dají vložit do [Injektáže závislostí (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory` lze použít k vytvoření instance `HttpClient`:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` tímto způsobem je dobrým způsobem, jak refaktorovat existující aplikaci. Nemá žádný vliv na způsob, jakým se používá `HttpClient`. V místech, kde jsou aktuálně vytvářeny `HttpClient` instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Pojmenovaná klienti

Pokud aplikace vyžaduje mnoho různých použití `HttpClient`, každá s jinou konfigurací, je možnost použít **pojmenované klienty**. Konfiguraci pro pojmenovanou `HttpClient` lze zadat během registrace v `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu je zavolána `AddHttpClient`, který poskytuje název *GitHub*. U tohoto klienta se používá některá výchozí konfigurace&mdash;konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.

Pokaždé, když je volána `CreateClient`, je vytvořena nová instance `HttpClient` a je volána akce konfigurace.

Chcete-li využívat pojmenovaného klienta, lze předat řetězcové parametry do `CreateClient`. Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Poskytněte jedno umístění pro konfiguraci a interakci s konkrétním `HttpClient`. Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.
* Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.

Typový klient přijímá parametr `HttpClient` ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu se konfigurace přesune do typovaného klienta. Objekt `HttpClient` je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které zpřístupňují funkce `HttpClient`. Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování na a analyzuje nejnovější otevřené problémy z úložiště GitHubu.

K registraci typovaného klienta lze použít metodu rozšíření Generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v rámci `Startup.ConfigureServices`a zadat typovou třídu klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Pokud je to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v `Startup.ConfigureServices`, nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné zapouzdřit `HttpClient` v rámci zadaného klienta. Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají instanci `HttpClient` interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu je `HttpClient` uložen jako soukromé pole. Veškerý přístup k externím voláním projde metodou `GetRepos`.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` lze použít v kombinaci s jinými knihovnami třetích stran, jako je například [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky `RestService`pomocí `HttpClient` k vytvoření externích volání HTTP.

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

`HttpClient` již obsahuje koncept delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory` usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta. Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler>. Před předáním požadavku další obslužné rutině v kanálu přepište metodu `SendAsync` pro spuštění kódu.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Kontroluje, zda byla v žádosti obsažena hlavička `X-API-KEY`. Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.

Během registrace lze do konfigurace `HttpClient`přidat jeden nebo více obslužných rutin. Tato úloha se provádí prostřednictvím rozšiřujících metod na <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu je `ValidateHeaderHandler` zaregistrován v DI. Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru. Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:

* Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.
* Vyřazené obslužné služby způsobí selhání obslužné rutiny.

Po zaregistrování můžete <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> volat a předat do něj typ obslužné rutiny.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties`.
* Pro přístup k aktuálnímu požadavku použijte `IHttpContextAccessor`.
* Vytvořte vlastní objekt úložiště `AsyncLocal`, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` se integruje s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými instancemi `HttpClient`. Rozšíření Polly:

* Podpora přidávání obslužných rutin na základě Polly do klientů.
* Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K většině běžných chyb dochází, když jsou externí volání HTTP přechodný. K dispozici je vhodná rozšiřující metoda s názvem `AddTransientHttpErrorPolicy`, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady konfigurované pomocí této metody rozšíření zpracovávají `HttpRequestException`, odpovědi HTTP 5xx a odpovědi HTTP 408.

Rozšíření `AddTransientHttpErrorPolicy` lze použít v `Startup.ConfigureServices`. Rozšíření poskytuje přístup k objektu `PolicyBuilder` nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu je definována zásada `WaitAndRetryAsync`. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly. Jedno takové rozšíření je `AddPolicyHandler`, které má více přetížení. Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá rozšíření `AddTransientHttpErrorPolicy` k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu pro přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je jejich definování jednou a jejich registrací pomocí `PolicyRegistry`. K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou při přidání `PolicyRegistry` do `ServiceCollection`zaregistrovány dvě zásady. Pokud chcete použít zásadu z registru, použije se `AddPolicyHandlerFromRegistry` metoda, která předává název zásady, která se má použít.

Další informace o integraci `IHttpClientFactory` a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová instance `HttpClient` se vrátí pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Je k dispozici <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta. Továrna spravuje životnost instancí `HttpMessageHandler`.

`IHttpClientFactory` fonduje `HttpMessageHandler` instance vytvořené továrnou, aby se snížila spotřeba prostředků. Při vytváření nové instance `HttpClient` se dá z fondu znovu použít instance `HttpMessageHandler`, pokud její životnost ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů. Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder`, který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta se nevyžaduje. Vyřazení zruší odchozí žádosti a zaručuje, že danou instanci `HttpClient` nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi. Instance `HttpClient` můžou být obecně ošetřené jako objekty .NET, které nevyžadují vyřazení.

Udržování jedné instance `HttpClient` aktivní pro dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory`. Tento model se po migraci na `IHttpClientFactory`nepotřebuje.

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhne:

* Problémy s vyčerpáním prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklem `HttpMessageHandler` instance v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající instance <xref:System.Net.Http.SocketsHttpHandler>.

- Vytvořte instanci `SocketsHttpHandler`, když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Podle potřeby vytvořte `HttpClient` instance pomocí `new HttpClient(handler, dispostHandler: false)`.

Předchozí přístupy vyřeší problémy správy prostředků, které `IHttpClientFactory` vyřešit podobným způsobem.

- `SocketsHttpHandler` sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler` zacykluje připojení podle `PooledConnectionLifetime`, aby se předešlo zastaralým problémům se službou DNS.

### <a name="cookies"></a>Soubory cookie

Instance `HttpMessageHandler` ve fondu mají za následek sdílení objektů `CookieContainer`. Neočekávané sdílení objektů `CookieContainer` často způsobuje nesprávný kód. U aplikací, které vyžadují soubory cookie, zvažte jednu z těchto akcí:

 - Zakázání automatického zpracování souborů cookie
 - Předcházení `IHttpClientFactory`

Voláním <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zakážete automatické zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>protokolování

Klienti vytvoření pomocí `IHttpClientFactory` zaznamenávají zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V *MyNamedClient* příkladu se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřních `HttpMessageHandler` používaných klientem.

Při přidávání pojmenovaných nebo typových klientů se vrátí `IHttpClientBuilder`. Metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného tímto klientem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient`. `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro spuštění metody `GetPage` služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
