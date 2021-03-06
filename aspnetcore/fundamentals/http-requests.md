---
title: Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core
author: stevejgordon
description: Přečtěte si o používání rozhraní IHttpClientFactory ke správě logických instancí HttpClient v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: ca52b6cf8646bced3a228341717f8ccb1edff582
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634199"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Provádění požadavků HTTP pomocí IHttpClientFactory v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Kirka](https://github.com/serpent5) Larkin

Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci. `IHttpClientFactory` nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Například klient s názvem  *GitHub* by mohl být zaregistrován a nakonfigurován pro přístup k [GitHubu](https://github.com/). Pro obecný přístup je možné zaregistrovat výchozího klienta.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` . Poskytuje rozšíření pro middleware založené na Polly k využití výhod delegování obslužných rutin v `HttpClient` .
* Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí. Automatická správa zabraňuje běžným problémům se službou DNS (Domain Name System), ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

Vzorový kód v tomto tématu verze používá <xref:System.Text.Json> k deserializaci obsahu JSON vráceného v odpovědích http. V případě ukázek, které používají `Json.NET` a `ReadAsAsync<T>` , použijte selektor verzí k výběru verze 2. x tohoto tématu.

## <a name="consumption-patterns"></a>Vzorce spotřeby

`IHttpClientFactory`V aplikaci lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Nejlepší přístup závisí na požadavcích aplikace.

### <a name="basic-usage"></a>Základní použití

`IHttpClientFactory` lze zaregistrovat voláním `AddHttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

`IHttpClientFactory`Může být požadováno pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection). Následující kód používá `IHttpClientFactory` k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` Like v předchozím příkladu je dobrým způsobem, jak refaktorovat existující aplikaci. Nemá žádný vliv na to, jak `HttpClient` se používá. Na místech `HttpClient` , kde jsou vytvořeny instance v existující aplikaci, nahraďte tyto výskyty voláními <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Pojmenovaná klienti

Pojmenovaná klienti jsou dobrou volbou v těchto případech:

* Aplikace vyžaduje mnoho různých použití `HttpClient` .
* Mnoho `HttpClient` s má odlišnou konfiguraci.

Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu je klient nakonfigurován pomocí nástroje:

* Základní adresa `https://api.github.com/` .
* Pro práci s rozhraním API GitHubu jsou nutná dvě záhlaví.

#### <a name="createclient"></a>CreateClient

Pokaždé, když <xref:System.Net.Http.IHttpClientFactory.CreateClient*> se zavolá:

* Vytvoří se nová instance `HttpClient` .
* Je volána akce konfigurace.

Chcete-li vytvořit pojmenovaného klienta, předejte jeho jméno do `CreateClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Kód může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` . Můžete například použít jednoho typu klienta:
  * Pro jeden koncový bod back-endu.
  * Zapouzdřit veškerou práci s koncovým bodem.
* Práce s DI a může být vložena tam, kde je to potřeba v aplikaci.

Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

V předchozím kódu:

* Konfigurace je přesunuta do typovaného klienta.
* `HttpClient`Objekt je vystaven jako veřejná vlastnost.

Je možné vytvořit metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkce. Například `GetAspNetDocsIssues` Metoda zapouzdřuje kód pro načtení otevřených problémů.

Následující kód volá <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> v `Startup.ConfigureServices` k registraci typové třídy klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. V předchozím kódu se `AddHttpClient` registruje `GitHubService` jako dočasná služba. Tato registrace používá metodu továrny k těmto účelům:

1. Vytvořte instanci `HttpClient`.
1. Vytvořte instanci objektu s `GitHubService` předáním v instanci `HttpClient` do jeho konstruktoru.

Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Konfiguraci pro typového klienta lze zadat během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient`Může být zapouzdřen v rámci typovaného klienta. Místo toho, abyste ho vystavili jako vlastnost, definujte metodu, která volá `HttpClient` interně instanci:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen v soukromém poli. Přístup k aplikaci `HttpClient` je veřejný `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` dá se použít v kombinaci s knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.

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

## <a name="make-post-put-and-delete-requests"></a>Vytváření požadavků POST, PUT a DELETE

V předchozích příkladech všechny požadavky HTTP používají příkaz GET HTTP. `HttpClient` podporuje také jiné příkazy HTTP, včetně:

* POST
* PUT
* DELETE
* POUŽITA

Úplný seznam podporovaných příkazů HTTP naleznete v tématu <xref:System.Net.Http.HttpMethod> .

Následující příklad ukazuje, jak vytvořit požadavek HTTP POST:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

V předchozím kódu `CreateItemAsync` metoda:

* Zaserializace `TodoItem` parametr do formátu JSON pomocí `System.Text.Json` . Tato operace používá instanci <xref:System.Text.Json.JsonSerializerOptions> ke konfiguraci procesu serializace.
* Vytvoří instanci <xref:System.Net.Http.StringContent> pro zabalení serializovaného JSON pro odeslání v těle požadavku HTTP.
* Volání <xref:System.Net.Http.HttpClient.PostAsync%2A> pro odeslání obsahu JSON na zadanou adresu URL. Toto je relativní adresa URL, která se přidá do [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).
* Volání <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> vyvolávající výjimku, pokud kód stavu odpovědi neindikuje úspěch.

`HttpClient` podporuje také další typy obsahu. Příklad: <xref:System.Net.Http.MultipartContent> a <xref:System.Net.Http.StreamContent>. Úplný seznam podporovaného obsahu najdete v tématu <xref:System.Net.Http.HttpContent> .

Následující příklad ukazuje požadavek HTTP PUT:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

Předchozí kód je velmi podobný jako příklad příspěvku. `SaveItemAsync`Metoda volá <xref:System.Net.Http.HttpClient.PutAsync%2A> místo `PostAsync` .

Následující příklad ukazuje požadavek HTTP DELETE:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

V předchozím kódu `DeleteItemAsync` metoda volá metodu <xref:System.Net.Http.HttpClient.DeleteAsync%2A> . Vzhledem k tomu, že žádosti o odstranění HTTP obvykle neobsahují žádné tělo, `DeleteAsync` Metoda neposkytne přetížení, které přijímá instanci `HttpContent` .

Další informace o používání různých příkazů HTTP s najdete `HttpClient` v tématu <xref:System.Net.Http.HttpClient> .

## <a name="outgoing-request-middleware"></a>Middleware odchozího požadavku

`HttpClient` má koncepci delegování obslužných rutin, které mohou být propojeny pro odchozí požadavky HTTP. `IHttpClientFactory`:

* Zjednodušuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta.
* Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model:

  * Se podobá příchozímu kanálu middleware v ASP.NET Core.
  * Poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, jako je například:

    * vyrovnávací
    * zpracování chyb
    * serializace
    * protokolování

Vytvoření obslužné rutiny delegování:

* Odvodit z <xref:System.Net.Http.DelegatingHandler> .
* Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*> . Před předáním požadavku další obslužné rutině v kanálu spusťte kód:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód zkontroluje, zda `X-API-KEY` je hlavička v žádosti. Pokud `X-API-KEY` chybí, <xref:System.Net.HttpStatusCode.BadRequest> je vrácena.

Do konfigurace pro se dá přidat víc než jedna obslužná rutina `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di. `IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu. Obslužné rutiny mohou záviset na službách jakéhokoli oboru. Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.

Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data do obslužné rutiny pomocí [zprávy HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Pro přístup k aktuálnímu požadavku použijte.
* Vytvořte vlastní <xref:System.Threading.AsyncLocal`1> objekt úložiště, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` integruje se s knihovnou [Polly](https://github.com/App-vNext/Polly)třetí strany. Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi. Rozšíření Polly podporují přidávání obslužných rutin založených na Polly do klientů. Polly vyžaduje balíček NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K chybám obvykle dochází, když jsou externí volání HTTP přechodný. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady nakonfigurované s nástrojem `AddTransientHttpErrorPolicy` zpracovávají následující odpovědi:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

V předchozím kódu `WaitAndRetryAsync` je definována zásada. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Rozšiřující metody jsou k dispozici pro přidání obslužných rutin na základě Polly, například <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> . Následující `AddPolicyHandler` přetížení zkontroluje požadavek na rozhodnutí, které zásady se mají použít:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu:

* Přidávají se dvě obslužné rutiny.
* První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát.
* Druhé `AddTransientHttpErrorPolicy` volání přidá zásadu pro přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud se 5 nezdařených pokusů vyskytnou sekvenčně. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` .

V následujícím kódu:

* Přidají se zásady "regular" a "Long".
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> Přidá do registru zásady "regular" a "Long".

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Další informace o `IHttpClientFactory` Polly integrech a integraci najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` . <xref:System.Net.Http.HttpMessageHandler>Vytvoří se pro jednotlivé pojmenované klienty. Továrna spravuje životnost `HttpMessageHandler` instancí.

`IHttpClientFactory` Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků. Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v reakce na změny DNS (Domain Name System).

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána podle pojmenovaných klientů:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient` instance je obecně možné považovat **za objekty .NET, které** nevyžadují vyřazení. Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi.

Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` . Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:

* Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.

- Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.

Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.

- `SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.

### <a name="no-loccookies"></a>Cookiepracují

Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů. Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:

 - Zákaz automatického cookie zpracování
 - Opakované `IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií "System .NET. http. HttpClient. **MyNamedClient**. LogicalHandler". Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V příkladu *MyNamedClient* jsou tyto zprávy protokolovány pomocí kategorie log "System .NET. http. HttpClient. **MyNamedClient**. ClientHandler". Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí nebo kód stavu odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.

`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` . `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware šíření hlaviček

Šíření hlaviček je ASP.NET Core middleware pro šíření hlaviček protokolu HTTP z příchozího požadavku do odchozích požadavků klienta HTTP. Použití rozšíření hlavičky:

* Odkázat na balíček [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .
* Konfigurace middlewaru a `HttpClient` v `Startup` :

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Další zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Postup serializace a deserializace JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)

Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/). Výchozí klient může být zaregistrován pro jiné účely.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.
* Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Vzorce spotřeby

`IHttpClientFactory`V aplikaci lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádná z nich není výhradně nadřízena jiným. Nejlepší přístup závisí na omezeních aplikace.

### <a name="basic-usage"></a>Základní použití

`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci. Nemá žádný vliv na způsob `HttpClient` použití. Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Pojmenovaná klienti

Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**. Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*. U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.

Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.

Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` . Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` . Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.
* Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.

Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu se konfigurace přesune do typovaného klienta. `HttpClient`Objekt je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost. `GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.

K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta. Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen jako soukromé pole. Veškerý přístup k externím voláním prochází `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.

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

`HttpClient` již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta. Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> . `SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička. Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.

Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` . Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di. `IHttpClientFactory`Vytvoří samostatný obor di pro každou obslužnou rutinu. Obslužné rutiny jsou zadarmo závislé na službách jakéhokoli oboru. Služby, na kterých obslužné rutiny závisejí, jsou uvolněny při uvolnění obslužné rutiny.

Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné ji volat s předáním typu pro obslužnou rutinu.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .
* `IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.
* Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi. Rozšíření Polly:

* Podpora přidávání obslužných rutin na základě Polly do klientů.
* Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K většině běžných chyb dochází, když jsou externí volání HTTP přechodný. `AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.

`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` . Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu `WaitAndRetryAsync` je definována zásada. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly. Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení. Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` . K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` . Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.

Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` . Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta. Továrna spravuje životnost `HttpMessageHandler` instancí.

`IHttpClientFactory` Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků. Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů. Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta se nevyžaduje. Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi. `HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.

Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` . Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:

* Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.

- Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.

Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.

- `SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.

### <a name="no-loccookies"></a>Cookiepracují

Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů. Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:

 - Zákaz automatického cookie zpracování
 - Opakované `IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.

`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` . `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Další zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Od [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak)a [Steve Gordon](https://github.com/stevejgordon)

Je <xref:System.Net.Http.IHttpClientFactory> možné zaregistrovat a použít ke konfiguraci a vytváření <xref:System.Net.Http.HttpClient> instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí. Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k [GitHubu](https://github.com/). Výchozí klient může být zaregistrován pro jiné účely.
* Kodifikovat koncept odchozího middleware prostřednictvím delegování obslužných rutin v `HttpClient` a poskytuje rozšíření pro middleware založené na Polly, které tuto funkci využívají.
* Spravuje sdružování a životnost základních `HttpClientMessageHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.
* Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Projekty, které cílí na .NET Framework vyžadují instalaci balíčku [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet. Projekty, které cílí na .NET Core a odkazují na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , už `Microsoft.Extensions.Http` balíček obsahuje.

## <a name="consumption-patterns"></a>Vzorce spotřeby

`IHttpClientFactory`V aplikaci lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Pojmenovaná klienti](#named-clients)
* [Typové klienti](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádná z nich není výhradně nadřízena jiným. Nejlepší přístup závisí na omezeních aplikace.

### <a name="basic-usage"></a>Základní použití

`IHttpClientFactory`Lze zaregistrovat voláním `AddHttpClient` metody rozšíření v `IServiceCollection` , uvnitř `Startup.ConfigureServices` metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci může kód přijmout `IHttpClientFactory` služby odkudkoli s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection). `IHttpClientFactory`Lze použít k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory`Tímto způsobem je vhodným způsobem refaktorovat existující aplikaci. Nemá žádný vliv na způsob `HttpClient` použití. Na místech `HttpClient` , kde jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Pojmenovaná klienti

Pokud aplikace vyžaduje mnoho různých použití `HttpClient` , každý s jinou konfigurací, je možnost používat **pojmenované klienty**. Konfiguraci pro název `HttpClient` lze zadat během registrace v `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` je volána metoda, která poskytuje název *GitHub*. U tohoto klienta se používá některá výchozí konfigurace, &mdash; konkrétně základní adresa a dvě hlavičky, které se vyžadují pro práci s rozhraním API GitHubu.

Pokaždé `CreateClient` , když se zavolá, vytvoří se nová instance `HttpClient` a pak se zavolá akce konfigurace.

Chcete-li využívat pojmenovaného klienta, lze předat parametr řetězce `CreateClient` . Zadejte název klienta, který se má vytvořit:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu nemusí požadavek určovat název hostitele. Může předat jenom cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Typové klienti

Klienti typu:

* Poskytněte stejné funkce jako pojmenované klienty, aniž by bylo nutné používat řetězce jako klíče.
* Poskytuje IntelliSense a pomocníka s kompilátorem při spotřebovávání klientů.
* Zadejte jedno umístění, které chcete nakonfigurovat, a pracujte s ním `HttpClient` . Například jediný typ klienta může být použit pro jeden koncový bod back-end a zapouzdření veškeré logiky v rámci tohoto koncového bodu.
* Práce s DI a může být vložena tam, kde je to potřeba ve vaší aplikaci.

Typový klient přijímá `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu se konfigurace přesune do typovaného klienta. `HttpClient`Objekt je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro rozhraní API, které zpřístupňují `HttpClient` funkčnost. `GetAspNetDocsIssues`Metoda zapouzdřuje kód potřebný k dotazování a analyzuje nejnovější otevřené problémy z úložiště GitHub.

K registraci typovaného klienta <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> lze použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` a určení typové třídy klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Typový klient je zaregistrován jako přechodný s DI. Typového klienta lze vložit a spotřebovat přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Je-li to preferováno, může být konfigurace pro zadaného klienta zadána během registrace v nástroji `Startup.ConfigureServices` , nikoli v konstruktoru typovaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné úplně zapouzdřit v `HttpClient` rámci zadaného klienta. Místo toho, aby jej vystavila jako vlastnost, lze poskytnout veřejné metody, které volají `HttpClient` instanci interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen jako soukromé pole. Veškerý přístup k externím voláním prochází `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory` dá se použít v kombinaci s jinými knihovnami třetích stran, jako je [REFIT](https://github.com/paulcbetts/refit). REFIT je knihovna REST pro .NET. Převede rozhraní REST API na živá rozhraní. Implementace rozhraní je vygenerována dynamicky pomocí `RestService` , a používá `HttpClient` se k vytvoření externích volání http.

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

`HttpClient` již má koncepci delegování obslužných rutin, které lze propojit společně pro odchozí požadavky HTTP. `IHttpClientFactory`Usnadňuje definování obslužných rutin, které se mají použít pro každého pojmenovaného klienta. Podporuje registraci a řetězení více obslužných rutin pro sestavení kanálu middleware odchozího požadavku. Každý z těchto obslužných rutin je schopný provést práci před odchozím požadavkem a po ní. Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core. Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, Definujte třídu odvozenou z <xref:System.Net.Http.DelegatingHandler> . `SendAsync`Před předáním požadavku další obslužné rutině v kanálu přepište metodu pro spuštění kódu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Kontroluje, zda byla do `X-API-KEY` žádosti vložena hlavička. Pokud záhlaví chybí, může se vyhnout volání HTTP a vracet vhodnou odpověď.

Během registrace lze do konfigurace pro přidat jeden nebo více obslužných rutin `HttpClient` . Tuto úlohu lze provést prostřednictvím rozšiřujících metod v <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu `ValidateHeaderHandler` je zaregistrována v di. Obslužná rutina **musí** být registrována v di jako přechodné služby bez oboru. Pokud je obslužná rutina registrována jako Oborová služba a všechny služby, na kterých závisí obslužná rutina, jsou jednorázové:

* Služby obslužné rutiny mohou být odstraněny před tím, než se obslužná rutina dostane mimo rozsah.
* Vyřazené obslužné služby způsobí selhání obslužné rutiny.

Po registraci je <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> možné je volat s předáním do typu obslužné rutiny.

Více obslužných rutin lze registrovat v pořadí, ve kterém by měly být spuštěny. Každá obslužná rutina zabalí další obslužnou rutinu, dokud poslední `HttpClientHandler` nespustí požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Ke sdílení stavu jednotlivých požadavků pomocí obslužných rutin zpráv použijte jeden z následujících přístupů:

* Předejte data obslužné rutině pomocí `HttpRequestMessage.Properties` .
* `IHttpContextAccessor`Pro přístup k aktuálnímu požadavku použijte.
* Vytvořte vlastní `AsyncLocal` objekt úložiště, který bude předávat data.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založené na Polly

`IHttpClientFactory` integruje se s oblíbenou knihovnou třetích stran s názvem [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodná knihovna pro zpracování chyb pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, přerušení okruhů, časový limit, izolaci přepážek a nouzové řešení v rámci Fluent a bezpečného přístupu z více vláken.

K dispozici jsou rozšiřující metody umožňující použití zásad Polly s konfigurovanými `HttpClient` instancemi. Rozšíření Polly:

* Podpora přidávání obslužných rutin na základě Polly do klientů.
* Dá se použít po instalaci balíčku NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) . Balíček není zahrnutý ve ASP.NET Core sdíleném rozhraní.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K většině běžných chyb dochází, když jsou externí volání HTTP přechodný. `AddTransientHttpErrorPolicy`Je zahrnutá vhodná rozšiřující metoda, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady konfigurované pomocí této obslužné rutiny metody rozšíření `HttpRequestException` , odpovědí HTTP 5xx a odpovědí http 408.

`AddTransientHttpErrorPolicy`Rozšíření lze použít v rámci `Startup.ConfigureServices` . Rozšíření poskytuje přístup k `PolicyBuilder` objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu `WaitAndRetryAsync` je definována zásada. U neúspěšných žádostí se Opakovaný pokus opakuje více než třikrát s prodlevou 600 MS mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamické výběry zásad

Existují další rozšiřující metody, které lze použít k přidání obslužných rutin založených na Polly. Jedno takové rozšíření je `AddPolicyHandler` , které má více přetížení. Jedno přetížení umožňuje, aby byl požadavek zkontrolován při definování zásady, která se má použít:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Pokud je v předchozím kódu odchozí požadavek HTTP GET, použije se časový limit 10 sekund. Pro jakoukoliv jinou metodu HTTP se použije časový limit 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořovat zásady Polly, které poskytují vylepšené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování. Neúspěšné požadavky se opakují třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá zásadu přerušení okruhu. Další externí požadavky se zablokují po dobu 30 sekund, pokud dojde k pěti pokusům o selhání po sobě. Zásady pro dělení na okruhy jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě často používaných zásad je definovat jednou a zaregistrovat je pomocí `PolicyRegistry` . K dispozici je rozšiřující metoda, která umožňuje přidání obslužné rutiny pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou při přidání do do. zaregistrovány dvě zásady `PolicyRegistry` `ServiceCollection` . Chcete-li použít zásadu z registru, `AddPolicyHandlerFromRegistry` je použita metoda, která předává název zásady, která se má použít.

Další informace o `IHttpClientFactory` integraci a Polly najdete na [wikiwebu Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>Správa HttpClient a životního cyklu

Nová `HttpClient` instance se vrátí pokaždé, když `CreateClient` se zavolá na `IHttpClientFactory` . Existuje <xref:System.Net.Http.HttpMessageHandler> pro každého pojmenovaného klienta. Továrna spravuje životnost `HttpMessageHandler` instancí.

`IHttpClientFactory` Vytvoří fondy `HttpMessageHandler` instancí vytvořených výrobou, aby se snížila spotřeba prostředků. Instance se dá `HttpMessageHandler` znovu použít z fondu při vytváření nové `HttpClient` instance, pokud její doba platnosti ještě nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní podkladová připojení HTTP. Vytváření dalších obslužných rutin, než je potřeba, může způsobit zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřené po neomezenou dobu, což může zabránit obslužné rutině v rekomunikaci se změnami DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána u jednotlivých pojmenovaných klientů. Chcete-li jej přepsat, zavolejte <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> na `IHttpClientBuilder` , který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Vyřazení klienta se nevyžaduje. Vyřazení zruší odchozí žádosti a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*> . `IHttpClientFactory` sleduje a odstraňuje prostředky používané `HttpClient` instancemi. `HttpClient`Instance je obecně možné považovat za objekty .NET, které nevyžadují vyřazení.

Udržování jediné `HttpClient` instance po dlouhou dobu je společný vzor, který se používá před zahájením `IHttpClientFactory` . Tento model se po migraci na nedá zbytečné `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou di se vyhne:

* Problémy s vyčerpáním prostředků díky sdružování `HttpMessageHandler` instancí.
* Zastaralé problémy se službou DNS cyklicky cyklických `HttpMessageHandler` instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí problémy pomocí dlouhotrvající <xref:System.Net.Http.SocketsHttpHandler> instance.

- Vytvořte instanci aplikace, `SocketsHttpHandler` když se aplikace spustí a použije se po celou dobu životnosti aplikace.
- Proveďte konfiguraci <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na odpovídající hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` podle potřeby.

Předchozí přístupy vyřeší problémy se správou prostředků, které se `IHttpClientFactory` podobným způsobem vyřeší.

- `SocketsHttpHandler`Sdílí připojení mezi `HttpClient` instancemi. Toto sdílení zabraňuje vyčerpání soketů.
- `SocketsHttpHandler`Cyklická připojení jsou na základě toho, `PooledConnectionLifetime` aby se předešlo zastaralým problémům s DNS.

### <a name="no-loccookies"></a>Cookiepracují

Instance ve fondu mají `HttpMessageHandler` za následek `CookieContainer` Sdílení objektů. Neočekávané `CookieContainer` Sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují cookie s, zvažte jednu z těchto akcí:

 - Zákaz automatického cookie zpracování
 - Opakované `IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pro zakázání automatického cookie zpracování:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvoření prostřednictvím `IHttpClientFactory` záznamu zprávy protokolu pro všechny požadavky. V konfiguraci protokolování povolte příslušnou úroveň informací, aby se zobrazily výchozí zprávy protokolu. Další protokolování, jako je protokolování hlaviček požadavků, je zahrnuté jenom na úrovni trasování.

Kategorie protokolu použitá pro každého klienta zahrnuje název klienta. Klient s názvem *MyNamedClient*například protokoluje zprávy s kategorií `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . Zprávy s příponou *LogicalHandler* se vyskytují mimo kanál obslužné rutiny žádosti. V žádosti se zprávy protokolují předtím, než je zpracuje kterákoli jiný obslužná rutina v kanálu. Na základě odpovědi se zprávy zaprotokolují, až ostatní obslužné rutiny kanálu obdrží odpověď.

K protokolování dojde také uvnitř kanálu obslužné rutiny žádosti. V příkladu *MyNamedClient* se tyto zprávy protokolují do kategorie protokolu `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . Pro požadavek se k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním žádosti v síti. V odpovědi Toto protokolování zahrnuje stav odpovědi předtím, než se přepošle zpět přes kanál obslužné rutiny.

Povolení protokolování mimo kanál a uvnitř kanálu umožňuje kontrolu změn provedených ostatními obslužnými rutinami kanálu. To může zahrnovat změny hlaviček žádostí, například nebo stavový kód odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolu pro konkrétní pojmenované klienty, pokud je to nutné.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace HttpMessageHandler

Může být nutné řídit konfiguraci vnitřního `HttpMessageHandler` používaného klienta.

`IHttpClientBuilder`Při přidávání pojmenovaných nebo typových klientů se vrátí. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Metodu rozšíření lze použít k definování delegáta. Delegát slouží k vytvoření a konfiguraci primárního `HttpMessageHandler` používaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití IHttpClientFactory v konzolové aplikaci

V aplikaci konzoly přidejte do projektu následující odkazy na balíček:

* [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory> je zaregistrován v kontejneru služby [obecného hostitele](xref:fundamentals/host/generic-host) .
* `MyService` Vytvoří instanci klientské továrny ze služby, která se používá k vytvoření `HttpClient` . `HttpClient` slouží k načtení webové stránky.
* `Main` vytvoří obor pro provedení `GetPage` metody služby a zapíše první 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware šíření hlaviček

Šíření hlaviček představuje middleware podporovanou komunitou pro šíření hlaviček protokolu HTTP z příchozího požadavku na odchozí požadavky klienta HTTP. Použití rozšíření hlavičky:

* Odkaz na podporovaný port komunity balíčku [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3,1 a novější podporuje [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Konfigurace middlewaru a `HttpClient` v `Startup` :

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Klient zahrnuje nakonfigurovaná záhlaví na odchozích žádostech:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Další zdroje informací

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů volání HTTP pomocí exponenciálního omezení rychlostiu se zásadami HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
