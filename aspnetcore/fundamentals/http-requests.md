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
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Vytváření požadavků HTTP pomocí aplikace IHttpClientFactory v ASP.NET jádru

::: moniker range=">= aspnetcore-3.0"

[Od Glenn condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Kirk Larkin](https://github.com/serpent5)

Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci. `IHttpClientFactory`nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí. Například klient s názvem *github* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/). Výchozího klienta lze zaregistrovat pro obecný přístup.
* Kodifikuje koncept odchozího middleware prostřednictvím delegování manipulátorů v `HttpClient`. Poskytuje rozšíření pro middleware založené na Polly, aby bylo `HttpClient`možné využít delegování obslužných rutin v .
* Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí. Automatická správa se vyhýbá běžným problémům dns (Domain `HttpClient` Name System), ke kterým dochází při ruční správě životnosti.
* Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)).

Ukázkový kód v této <xref:System.Text.Json> verzi tématu používá k rekonstrukci obsahu JSON vrácenév odpovědi HTTP. Pro ukázky, které používají `Json.NET` a `ReadAsAsync<T>`, použijte volič verze pro výběr verze tohoto tématu.

## <a name="consumption-patterns"></a>Spotřeby

V aplikaci `IHttpClientFactory` lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Jmenovaní klienti](#named-clients)
* [Klienti zadali](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Nejlepší přístup závisí na požadavcích aplikace.

### <a name="basic-usage"></a>Základní použití

`IHttpClientFactory`lze zaregistrovat `AddHttpClient`na telefonním čísle :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Lze `IHttpClientFactory` požádat pomocí [vkládání závislostí (DI)](xref:fundamentals/dependency-injection). Následující kód `IHttpClientFactory` používá k `HttpClient` vytvoření instance:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` jako v předchozím příkladu je dobrý způsob, jak refaktorovat existující aplikaci. Nemá žádný vliv `HttpClient` na to, jak se používá. V místech, kde `HttpClient` jsou instance vytvořeny v existující aplikaci, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Jmenovaní klienti

Jmenovaní klienti jsou dobrou volbou, když:

* Aplikace vyžaduje mnoho různých `HttpClient`použití .
* Mnoho `HttpClient`s mají jinou konfiguraci.

Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu je klient nakonfigurován pomocí:

* Základní adresa `https://api.github.com/`.
* Dvě záhlaví potřebné pro práci s rozhraním API GitHub.

#### <a name="createclient"></a>Vytvořit klienta

Pokaždé <xref:System.Net.Http.IHttpClientFactory.CreateClient*> se nazývá:

* Je vytvořena `HttpClient` nová instance.
* Akce konfigurace se nazývá.

Chcete-li vytvořit pojmenovaného klienta, přejděte jeho název do `CreateClient`:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu požadavek není nutné zadat název hostitele. Kód může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Klienti zadali

Zadali klienti:

* Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.
* Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.
* Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým . Může být například použit jeden zadaný klient:
  * Pro jeden koncový bod back-endu.
  * Zapouzdřit všechny logiky zabývající se koncovým bodem.
* Práce s DI a může být aplikován, pokud je to požadováno v aplikaci.

Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

V předchozím kódu:

* Konfigurace je přesunuta do zadaného klienta.
* Objekt `HttpClient` je vystaven jako veřejná vlastnost.

Specifické metody rozhraní API mohou `HttpClient` být vytvořeny, které zveřejňují funkce. Například `GetAspNetDocsIssues` metoda zapouzdřuje kód pro načtení otevřených problémů.

Následující kód <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> volá `Startup.ConfigureServices` v zaregistrovat zadali třídy klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Zadaný klient je registrován jako přechodný s DI. V předchozím kódu `AddHttpClient` registruje `GitHubService` jako přechodné služby. Tato registrace používá metodu výroby k:

1. Vytvořte instanci `HttpClient`.
1. Vytvořte instanci `GitHubService`, předávání `HttpClient` v instanci jeho konstruktoru.

Zadaný klient může být injektován a spotřebován přímo:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Konfigurace pro zadaného klienta může být `Startup.ConfigureServices`zadána při registraci v , nikoli v konstruktoru zadaného klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Lze `HttpClient` zapouzdřit v rámci zadaného klienta. Spíše než vystavit jako vlastnost, definujte metodu, která volá `HttpClient` instanci interně:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen v soukromém poli. Přístup k `HttpClient` is veřejnou `GetRepos` metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory`lze použít v kombinaci s knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit). Refit je knihovna REST pro rozhraní .NET. Převádí rozhraní REST API na živá rozhraní. Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.

Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:

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

Zadaný klient lze přidat pomocí Refit generovat implementaci:

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

Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware odchozí žádosti

`HttpClient`má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP. `IHttpClientFactory`:

* Zjednodušuje definování obslužných rutin, které mají být aplikovány pro každého pojmenovaného klienta.
* Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek. Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek. Tento vzor:

  * Je podobný příchozí middleware potrubí v ASP.NET Core.
  * Poskytuje mechanismus pro správu průřezových problémů týkajících se požadavků HTTP, například:

    * Mezipaměti
    * zpracování chyb
    * Serializace
    * protokolování

Vytvoření delegující obslužné rutiny:

* Odvodit z <xref:System.Net.Http.DelegatingHandler>.
* Přepsat <xref:System.Net.Http.DelegatingHandler.SendAsync*>. Spusťte kód před předáním požadavku další obslužné rutině v kanálu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód zkontroluje, `X-API-KEY` zda je hlavička v požadavku. Pokud `X-API-KEY` chybí, <xref:System.Net.HttpStatusCode.BadRequest> je vrácena.

Do konfigurace pro `HttpClient` : <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

V předchozím kódu `ValidateHeaderHandler` je registrován s DI. Vytvoří `IHttpClientFactory` samostatný obor DI pro každou obslužnou rutinu. Obslužné rutiny mohou záviset na služby libovolného oboru. Služby, které jsou závislé na jsou uvolněny při obslužné rutiny je uvolněna.

Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.

Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést. Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:

* Předejte data do obslužné rutiny pomocí [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).
* Slouží <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> k přístupu k aktuálnímu požadavku.
* Vytvořte <xref:System.Threading.AsyncLocal`1> vlastní objekt úložiště pro předání dat.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založených na Polly

`IHttpClientFactory`integruje s knihovnou třetí [strany Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.

Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi. Rozšíření Polly podporují přidání obslužné rutiny založené na Polly klientům. Polly vyžaduje balíček [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

Chyby obvykle dojít, když externí volání HTTP jsou přechodné. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady `AddTransientHttpErrorPolicy` nakonfigurované s popisovačem následující odpovědi:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* PROTOKOL HTTP 408

`AddTransientHttpErrorPolicy`poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

V předchozím kódu je `WaitAndRetryAsync` definována zásada. Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamicky vybrané zásady

Rozšiřující metody jsou k dispozici pro přidání polly <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>založené obslužné rutiny, například . Následující `AddPolicyHandler` přetížení kontroluje požadavek rozhodnout, které zásady použít:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund. Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořit Polly politiky:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu:

* Jsou přidány dvě obslužné rutiny.
* První obslužná rutina používá <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> k přidání zásady opakování. Neúspěšné požadavky jsou opakovány až třikrát.
* Druhé `AddTransientHttpErrorPolicy` volání přidá zásady jističe. Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k 5 neúspěšným pokusům postupně. Zásady jističe jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s .

V následujícím kódu:

* "Pravidelné" a "dlouhé" police jsou přidány.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>přidá "pravidelné" a "dlouhé" zásady z registru.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient a správa životnosti

Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Je <xref:System.Net.Http.HttpMessageHandler> vytvořen pro pojmenované klienta. Továrna spravuje `HttpMessageHandler` životnostininstance.

`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků. Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP. Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS (Domain Name System).

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána na základě pojmenovaného klienta:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`instance lze obecně považovat za objekty .NET, které **nevyžadují** vyřazení. Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi.

Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem . Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:

* Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.

- Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.

Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.

- Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi. Toto sdílení zabraňuje vyčerpání soketu.
- Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.

### <a name="cookies"></a>Soubory cookie

Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů. Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují soubory cookie, zvažte buď:

 - Zakázání automatického zpracování souborů cookie
 - Vyhnout`IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky. Povolte příslušnou úroveň informací v konfiguraci protokolování, abyste viděli výchozí zprávy protokolu. Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.

Kategorie protokolu použitá pro každého klienta obsahuje název klienta. Klient s názvem *MyNamedClient*, například protokoluje zprávy s kategorií "System.Net.Http.HttpClient. **MyNamedClient**. LogicalHandler". Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku. Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly. Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.

Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku. V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány s kategorií protokolu "System.Net.Http.Http.HttpClient. **MyNamedClient**. ClientHandler". Pro požadavek k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku. Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.

Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu. To může zahrnovat změny hlaviček požadavků nebo stavového kódu odpovědi.

Zahrnutí názvu klienta do kategorie protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace httpmessagehandleru

Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.

Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta. Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití ihttpclientfactory v konzolové aplikaci

V konzolové aplikaci přidejte do projektu následující odkazy na balíček:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)
* `MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient` `HttpClient`se používá k načtení webové stránky.
* `Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware šíření záhlaví

Šíření záhlaví je middleware ASP.NET Core k šíření hlaviček HTTP z příchozího požadavku na odchozí požadavky klienta HTTP. Použití šíření záhlaví:

* Odkaz na balíček [Microsoft.AspNetCore.HeaderPropagation.](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)
* Konfigurace middlewaru `HttpClient` `Startup`a v :

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* Klient obsahuje nakonfigurované hlavičky pro odchozí požadavky:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Další zdroje

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [Serializace a deserializace zařízení JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), a [Steve Gordon](https://github.com/stevejgordon)

Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí. Například *klient githubu* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/). Výchozího klienta lze zaregistrovat pro jiné účely.
* Kodifikuje koncept odchozí middleware prostřednictvím delegování obslužné rutiny a `HttpClient` poskytuje rozšíření pro Polly-založené middleware využít.
* Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.
* Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="consumption-patterns"></a>Spotřeby

V aplikaci `IHttpClientFactory` lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Jmenovaní klienti](#named-clients)
* [Klienti zadali](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádný z nich není striktně lepší než jiný. Nejlepší přístup závisí na omezení aplikace.

### <a name="basic-usage"></a>Základní použití

Lze `IHttpClientFactory` zaregistrovat voláním `AddHttpClient` metody rozšíření `IServiceCollection`na `Startup.ConfigureServices` , uvnitř metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci, kód `IHttpClientFactory` může přijmout služby kdekoli lze vložit [s vkládání závislostí (DI)](xref:fundamentals/dependency-injection). Slouží `IHttpClientFactory` k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` tímto způsobem je dobrý způsob, jak refaktorovat existující aplikaci. To nemá žádný vliv `HttpClient` na způsob, jakým se používá. V místech, kde `HttpClient` jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Jmenovaní klienti

Pokud aplikace vyžaduje mnoho `HttpClient`různých použití , každý s jinou konfigurací, možnost je použít **pojmenované klienty**. Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` se nazývá, zadání názvu *github*. Tento klient má některé&mdash;výchozí konfigurace použít konkrétně základní adresu a dvě hlavičky potřebné pro práci s Rozhraní matem GitHub.

Pokaždé, `CreateClient` když je volána, `HttpClient` je vytvořena nová instance a je volána akce konfigurace.

Chcete-li spotřebovat pojmenovaného klienta, `CreateClient`může být parametr řetězce předán společnosti . Zadejte název klienta, který má být vytvořen:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu požadavek není nutné zadat název hostitele. Může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Klienti zadali

Zadali klienti:

* Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.
* Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.
* Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým . Například jeden zadaný klient může být použit pro jeden koncový bod back-endu a zapouzdřit všechny logiky zabývající se tímto koncovým bodem.
* Pracujte s DI a můžete se v aplikaci injektovat tam, kde je to požadováno.

Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu je konfigurace přesunuta do zadaného klienta. Objekt `HttpClient` je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro `HttpClient` rozhraní API, které zveřejňují funkce. Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování a analyzovat nejnovější otevřené problémy z úložiště GitHub.

Chcete-li zaregistrovat zadali <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, lze `Startup.ConfigureServices`použít metodu obecné rozšíření v rámci , určení zadané třídy klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Zadaný klient je registrován jako přechodný s DI. Zadaný klient může být injektován a spotřebován přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Pokud je to preferováno, konfigurace pro zadaného klienta může být zadána při registraci v `Startup.ConfigureServices`aplikace , nikoli v konstruktoru zadaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné zcela zapouzdřit `HttpClient` v rámci zadaného klienta. Spíše než vystavení jako vlastnost, veřejné metody mohou být `HttpClient` poskytnuty, které volají instanci interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen jako soukromé pole. Veškerý přístup k externím `GetRepos` voláním prochází metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory`lze použít v kombinaci s jinými knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit). Refit je knihovna REST pro rozhraní .NET. Převádí rozhraní REST API na živá rozhraní. Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.

Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:

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

Zadaný klient lze přidat pomocí Refit generovat implementaci:

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

Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware odchozí žádosti

`HttpClient`již má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP. Usnadňuje `IHttpClientFactory` definování obslužné rutiny použít pro každý pojmenovaný klient. Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek. Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek. Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core. Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, definujte třídu odvozenou z . <xref:System.Net.Http.DelegatingHandler> Přepište `SendAsync` metodu spuštění kódu před předáním požadavku další obslužné rutině v kanálu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Zkontroluje, zda `X-API-KEY` byla do požadavku zahrnuta hlavička. Pokud záhlaví chybí, může se vyhnout volání HTTP a vrátit vhodnou odpověď.

Během registrace lze do konfigurace pro `HttpClient`. Tento úkol je proveden pomocí <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>metod rozšíření na .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu `ValidateHeaderHandler` je registrován s DI. Vytvoří `IHttpClientFactory` samostatný obor DI pro každou obslužnou rutinu. Obslužné rutiny mohou záviset na službách libovolného rozsahu. Služby, které jsou závislé na jsou uvolněny při obslužné rutiny je uvolněna.

Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.

Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést. Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:

* Předávat data do `HttpRequestMessage.Properties`obslužné rutiny pomocí .
* Slouží `IHttpContextAccessor` k přístupu k aktuálnímu požadavku.
* Vytvořte `AsyncLocal` vlastní objekt úložiště pro předání dat.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založených na Polly

`IHttpClientFactory`integruje s populární knihovnou třetích stran nazvanou [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.

Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi. Rozšíření Polly:

* Podporujte přidávání obslužných rutin založených na Polly klientům.
* Lze použít po instalaci balíčku [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet. Balíček není součástí sdíleného rámce ASP.NET Core.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K nejběžnějším chybám dochází, když jsou externí volání HTTP přechodná. Je zahrnuta `AddTransientHttpErrorPolicy` praktická metoda rozšíření, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady nakonfigurované s tímto popisovačem `HttpRequestException`metody rozšíření , odpovědi HTTP 5xx a odpovědi HTTP 408.

Rozšíření `AddTransientHttpErrorPolicy` lze použít `Startup.ConfigureServices`v rámci . Rozšíření poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu je `WaitAndRetryAsync` definována zásada. Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamicky vybrané zásady

Existují další metody rozšíření, které lze použít k přidání obslužných rutin založených na Polly. Jedním z `AddPolicyHandler`takových rozšíření je , který má více přetížení. Jedno přetížení umožňuje požadavek, který má být zkontrolován při definování zásad, které mají být aplikovány:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund. Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořit Polly zásady poskytovat rozšířené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování. Neúspěšné požadavky jsou opakovány až třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá jistič zásady. Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k pěti neúspěšným pokusům postupně. Zásady jističe jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s . Je k dispozici metoda rozšíření, která umožňuje obslužnou rutinu přidat pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou registrovány dvě `PolicyRegistry` zásady `ServiceCollection`při přidání do . Chcete-li použít zásadu `AddPolicyHandlerFromRegistry` z registru, metoda se používá, předávání název zásady použít.

Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient a správa životnosti

Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Je tu <xref:System.Net.Http.HttpMessageHandler> jeden z klientů. Továrna spravuje `HttpMessageHandler` životnostininstance.

`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků. Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP. Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána na základě pojmenovaného klienta. Chcete-li jej <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> přepsat, `IHttpClientBuilder` volání, který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Likvidace klienta není nutná. Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi. Instance `HttpClient` lze obecně považovat za objekty .NET, které nevyžadují vyřazení.

Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem . Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:

* Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.

- Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.

Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.

- Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi. Toto sdílení zabraňuje vyčerpání soketu.
- Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.

### <a name="cookies"></a>Soubory cookie

Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů. Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují soubory cookie, zvažte buď:

 - Zakázání automatického zpracování souborů cookie
 - Vyhnout`IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky. Povolte příslušnou úroveň informací v konfiguraci protokolování a zostřikněte výchozí zprávy protokolu. Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.

Kategorie protokolu použitá pro každého klienta obsahuje název klienta. Klient s názvem *MyNamedClient*, například protokoluje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`zprávy s kategorií . Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku. Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly. Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.

Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku. V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`proti kategorii protokolu . U požadavku k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku v síti. Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.

Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu. To může zahrnovat například změny hlaviček požadavků nebo stavového kódu odpovědi.

Zahrnutí názvu klienta v kategorii protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty v případě potřeby.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace httpmessagehandleru

Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.

Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta. Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití ihttpclientfactory v konzolové aplikaci

V konzolové aplikaci přidejte do projektu následující odkazy na balíček:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)
* `MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient` `HttpClient`se používá k načtení webové stránky.
* `Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Další zdroje

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), a [Steve Gordon](https://github.com/stevejgordon)

Můžete <xref:System.Net.Http.IHttpClientFactory> zaregistrovat a použít ke <xref:System.Net.Http.HttpClient> konfiguraci a vytvoření instancí v aplikaci. Nabízí následující výhody:

* Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí. Například *klient githubu* může být registrován a nakonfigurován pro přístup k [GitHubu](https://github.com/). Výchozího klienta lze zaregistrovat pro jiné účely.
* Kodifikuje koncept odchozí middleware prostřednictvím delegování obslužné rutiny a `HttpClient` poskytuje rozšíření pro Polly-založené middleware využít.
* Spravuje sdružování a `HttpClientMessageHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.
* Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

Projekty zaměřené na rozhraní .NET Framework vyžadují instalaci balíčku [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet. Projekty, které cílí na jádro .NET a odkazují na `Microsoft.Extensions.Http` [metabalíček Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app) již balíček obsahují.

## <a name="consumption-patterns"></a>Spotřeby

V aplikaci `IHttpClientFactory` lze použít několik způsobů:

* [Základní použití](#basic-usage)
* [Jmenovaní klienti](#named-clients)
* [Klienti zadali](#typed-clients)
* [Vygenerované klienty](#generated-clients)

Žádný z nich není striktně lepší než jiný. Nejlepší přístup závisí na omezení aplikace.

### <a name="basic-usage"></a>Základní použití

Lze `IHttpClientFactory` zaregistrovat voláním `AddHttpClient` metody rozšíření `IServiceCollection`na `Startup.ConfigureServices` , uvnitř metody.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Po registraci, kód `IHttpClientFactory` může přijmout služby kdekoli lze vložit [s vkládání závislostí (DI)](xref:fundamentals/dependency-injection). Slouží `IHttpClientFactory` k vytvoření `HttpClient` instance:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Použití `IHttpClientFactory` tímto způsobem je dobrý způsob, jak refaktorovat existující aplikaci. To nemá žádný vliv `HttpClient` na způsob, jakým se používá. V místech, kde `HttpClient` jsou aktuálně vytvořeny instance, nahraďte tyto výskyty voláním <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Jmenovaní klienti

Pokud aplikace vyžaduje mnoho `HttpClient`různých použití , každý s jinou konfigurací, možnost je použít **pojmenované klienty**. Konfiguraci pojmenované `HttpClient` položky lze `Startup.ConfigureServices`zadat při registraci v .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

V předchozím kódu `AddHttpClient` se nazývá, zadání názvu *github*. Tento klient má některé&mdash;výchozí konfigurace použít konkrétně základní adresu a dvě hlavičky potřebné pro práci s Rozhraní matem GitHub.

Pokaždé, `CreateClient` když je volána, `HttpClient` je vytvořena nová instance a je volána akce konfigurace.

Chcete-li spotřebovat pojmenovaného klienta, `CreateClient`může být parametr řetězce předán společnosti . Zadejte název klienta, který má být vytvořen:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

V předchozím kódu požadavek není nutné zadat název hostitele. Může předat pouze cestu, protože se používá základní adresa nakonfigurovaná pro klienta.

### <a name="typed-clients"></a>Klienti zadali

Zadali klienti:

* Poskytněte stejné možnosti jako jmenovaní klienti bez nutnosti používat řetězce jako klíče.
* Poskytuje pomoc technologie IntelliSense a kompilátoru při využívání klientů.
* Poskytněte jediné umístění pro `HttpClient`konfiguraci a interakci s určitým . Například jeden zadaný klient může být použit pro jeden koncový bod back-endu a zapouzdřit všechny logiky zabývající se tímto koncovým bodem.
* Pracujte s DI a můžete se v aplikaci injektovat tam, kde je to požadováno.

Zadaný klient přijme `HttpClient` parametr ve svém konstruktoru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

V předchozím kódu je konfigurace přesunuta do zadaného klienta. Objekt `HttpClient` je vystaven jako veřejná vlastnost. Je možné definovat metody specifické pro `HttpClient` rozhraní API, které zveřejňují funkce. Metoda `GetAspNetDocsIssues` zapouzdřuje kód potřebný k dotazování a analyzovat nejnovější otevřené problémy z úložiště GitHub.

Chcete-li zaregistrovat zadali <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> klienta, lze `Startup.ConfigureServices`použít metodu obecné rozšíření v rámci , určení zadané třídy klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Zadaný klient je registrován jako přechodný s DI. Zadaný klient může být injektován a spotřebován přímo:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Pokud je to preferováno, konfigurace pro zadaného klienta může být zadána při registraci v `Startup.ConfigureServices`aplikace , nikoli v konstruktoru zadaného klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Je možné zcela zapouzdřit `HttpClient` v rámci zadaného klienta. Spíše než vystavení jako vlastnost, veřejné metody mohou být `HttpClient` poskytnuty, které volají instanci interně.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

V předchozím kódu `HttpClient` je uložen jako soukromé pole. Veškerý přístup k externím `GetRepos` voláním prochází metodou.

### <a name="generated-clients"></a>Vygenerované klienty

`IHttpClientFactory`lze použít v kombinaci s jinými knihovnami třetích stran, jako je [Refit](https://github.com/paulcbetts/refit). Refit je knihovna REST pro rozhraní .NET. Převádí rozhraní REST API na živá rozhraní. Implementace rozhraní je generována dynamicky `RestService`pomocí `HttpClient` , pomocí externí volání HTTP.

Rozhraní a odpověď jsou definovány tak, aby představovaly externí rozhraní API a jeho odpověď:

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

Zadaný klient lze přidat pomocí Refit generovat implementaci:

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

Definované rozhraní může být v případě potřeby spotřebováno s implementací poskytovanou DI a Refit:

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

## <a name="outgoing-request-middleware"></a>Middleware odchozí žádosti

`HttpClient`již má koncept delegování obslužné rutiny, které mohou být propojeny pro odchozí požadavky HTTP. Usnadňuje `IHttpClientFactory` definování obslužné rutiny použít pro každý pojmenovaný klient. Podporuje registraci a řetězení více obslužných rutin k vytvoření kanálu middleware odchozí požadavek. Každý z těchto obslužných rutin je schopen provádět práci před a po odchozí požadavek. Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core. Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.

Chcete-li vytvořit obslužnou rutinu, definujte třídu odvozenou z . <xref:System.Net.Http.DelegatingHandler> Přepište `SendAsync` metodu spuštění kódu před předáním požadavku další obslužné rutině v kanálu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Předchozí kód definuje základní obslužnou rutinu. Zkontroluje, zda `X-API-KEY` byla do požadavku zahrnuta hlavička. Pokud záhlaví chybí, může se vyhnout volání HTTP a vrátit vhodnou odpověď.

Během registrace lze do konfigurace pro `HttpClient`. Tento úkol je proveden pomocí <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>metod rozšíření na .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

V předchozím kódu `ValidateHeaderHandler` je registrován s DI. Obslužná rutina **musí** být registrována v DI jako přechodná služba, nikdy scoped. Pokud je obslužná rutina registrována jako služba s vymezeným oborem a všechny služby, na kterých je obslužná rutina závislá, jsou na jedno použití:

* Služby obslužné rutiny může být uvolněna před obslužná rutina přejde mimo rozsah.
* Vyřazené služby obslužné rutiny způsobí selhání obslužné rutiny.

Po registraci, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> lze volat, předávání v typu obslužné rutiny.

Více obslužné rutiny mohou být registrovány v pořadí, které by měly provést. Každá obslužná rutina `HttpClientHandler` zalomí další obslužnou rutinu, dokud konečné neprovede požadavek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Pomocí jednoho z následujících přístupů můžete sdílet stav požadavku s obslužnými rutinami zpráv:

* Předávat data do `HttpRequestMessage.Properties`obslužné rutiny pomocí .
* Slouží `IHttpContextAccessor` k přístupu k aktuálnímu požadavku.
* Vytvořte `AsyncLocal` vlastní objekt úložiště pro předání dat.

## <a name="use-polly-based-handlers"></a>Použití obslužných rutin založených na Polly

`IHttpClientFactory`integruje s populární knihovnou třetích stran nazvanou [Polly](https://github.com/App-vNext/Polly). Polly je komplexní odolnost a přechodné zpracování chyb knihovny pro .NET. Umožňuje vývojářům vyjádřit zásady, jako je opakování, jistič, časový čas, přepážka izolace a nouzové způsobem.

Rozšiřující metody jsou k dispozici k povolení použití `HttpClient` Polly zásad s nakonfigurovanými instancemi. Rozšíření Polly:

* Podporujte přidávání obslužných rutin založených na Polly klientům.
* Lze použít po instalaci balíčku [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet. Balíček není součástí sdíleného rámce ASP.NET Core.

### <a name="handle-transient-faults"></a>Zpracování přechodných chyb

K nejběžnějším chybám dochází, když jsou externí volání HTTP přechodná. Je zahrnuta `AddTransientHttpErrorPolicy` praktická metoda rozšíření, která umožňuje definovat zásadu pro zpracování přechodných chyb. Zásady nakonfigurované s tímto popisovačem `HttpRequestException`metody rozšíření , odpovědi HTTP 5xx a odpovědi HTTP 408.

Rozšíření `AddTransientHttpErrorPolicy` lze použít `Startup.ConfigureServices`v rámci . Rozšíření poskytuje přístup `PolicyBuilder` k objektu nakonfigurovanému pro zpracování chyb představujících možnou přechodnou chybu:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

V předchozím kódu je `WaitAndRetryAsync` definována zásada. Neúspěšné požadavky jsou opakovány až třikrát se zpožděním 600 ms mezi pokusy.

### <a name="dynamically-select-policies"></a>Dynamicky vybrané zásady

Existují další metody rozšíření, které lze použít k přidání obslužných rutin založených na Polly. Jedním z `AddPolicyHandler`takových rozšíření je , který má více přetížení. Jedno přetížení umožňuje požadavek, který má být zkontrolován při definování zásad, které mají být aplikovány:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

V předchozím kódu, pokud odchozí požadavek je HTTP GET, je použit časový výta10 sekund. Pro všechny ostatní metody HTTP se používá časový čas 30 sekund.

### <a name="add-multiple-polly-handlers"></a>Přidání více obslužných rutin Polly

Je běžné vnořit Polly zásady poskytovat rozšířené funkce:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

V předchozím příkladu jsou přidány dvě obslužné rutiny. První používá `AddTransientHttpErrorPolicy` rozšíření k přidání zásady opakování. Neúspěšné požadavky jsou opakovány až třikrát. Druhé volání `AddTransientHttpErrorPolicy` přidá jistič zásady. Další externí požadavky jsou blokovány po dobu 30 sekund, pokud dojde k pěti neúspěšným pokusům postupně. Zásady jističe jsou stavové. Všechna volání prostřednictvím tohoto klienta sdílejí stejný stav okruhu.

### <a name="add-policies-from-the-polly-registry"></a>Přidání zásad z registru Polly

Přístup ke správě pravidelně používaných zásad je definovat `PolicyRegistry`je jednou a zaregistrovat je s . Je k dispozici metoda rozšíření, která umožňuje obslužnou rutinu přidat pomocí zásady z registru:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

V předchozím kódu jsou registrovány dvě `PolicyRegistry` zásady `ServiceCollection`při přidání do . Chcete-li použít zásadu `AddPolicyHandlerFromRegistry` z registru, metoda se používá, předávání název zásady použít.

Další informace `IHttpClientFactory` o integracích a integracích Polly naleznete na [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient a správa životnosti

Nová `HttpClient` instance je vrácena pokaždé, když `CreateClient` je volána na `IHttpClientFactory`. Je tu <xref:System.Net.Http.HttpMessageHandler> jeden z klientů. Továrna spravuje `HttpMessageHandler` životnostininstance.

`IHttpClientFactory`sdružuje `HttpMessageHandler` instance vytvořené výrobcem, aby se snížila spotřeba prostředků. Instance `HttpMessageHandler` může být znovu použita z `HttpClient` fondu při vytváření nové instance, pokud její životnost nevypršela.

Sdružování obslužných rutin je žádoucí, protože každá obslužná rutina obvykle spravuje vlastní základní připojení HTTP. Vytvoření více obslužných rutin, než je nutné, může mít za následek zpoždění připojení. Některé obslužné rutiny také udržují připojení otevřená po neomezenou dobu, což může zabránit tomu, aby obslužná rutina reagovala na změny DNS.

Výchozí životnost obslužné rutiny je dvě minuty. Výchozí hodnota může být přepsána na základě pojmenovaného klienta. Chcete-li jej <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> přepsat, `IHttpClientBuilder` volání, který je vrácen při vytváření klienta:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Likvidace klienta není nutná. Vyřazení zruší odchozí požadavky a zaručuje, že danou `HttpClient` instanci nelze použít po volání <xref:System.IDisposable.Dispose*>. `IHttpClientFactory`sleduje a odstraňuje prostředky `HttpClient` používané instancemi. Instance `HttpClient` lze obecně považovat za objekty .NET, které nevyžadují vyřazení.

Udržování jedné `HttpClient` instance naživu po dlouhou dobu je běžný vzor používaný `IHttpClientFactory`před vznikem . Tento vzor se stane zbytečné `IHttpClientFactory`po migraci do .

### <a name="alternatives-to-ihttpclientfactory"></a>Alternativy k IHttpClientFactory

Použití `IHttpClientFactory` v aplikaci s podporou DI se vyhýbá:

* Problémy vyčerpání prostředků sdružováním `HttpMessageHandler` instancí.
* Zastaralé problémy DNS `HttpMessageHandler` pomocí cyklických instancí v pravidelných intervalech.

Existují alternativní způsoby, jak vyřešit předchozí <xref:System.Net.Http.SocketsHttpHandler> problémy pomocí instance s dlouhou životností.

- Vytvořte instanci toho, `SocketsHttpHandler` kdy se aplikace spustí, a použijte ji po celou dobu životnosti aplikace.
- Nakonfigurujte <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> na příslušnou hodnotu na základě časů aktualizace DNS.
- Vytvořte `HttpClient` instance `new HttpClient(handler, disposeHandler: false)` pomocí podle potřeby.

Předchozí přístupy řeší problémy `IHttpClientFactory` se správou zdrojů, které řeší podobným způsobem.

- Sdílí `SocketsHttpHandler` připojení `HttpClient` mezi instancemi. Toto sdílení zabraňuje vyčerpání soketu.
- Cyklické `SocketsHttpHandler` připojení `PooledConnectionLifetime` podle, aby se zabránilo zastaralé problémy DNS.

### <a name="cookies"></a>Soubory cookie

Sdružené `HttpMessageHandler` instance `CookieContainer` má za následek sdílení objektů. Neočekávané `CookieContainer` sdílení objektů často vede k nesprávnému kódu. U aplikací, které vyžadují soubory cookie, zvažte buď:

 - Zakázání automatického zpracování souborů cookie
 - Vyhnout`IHttpClientFactory`

Volání <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> zaúčelem zakázání automatického zpracování souborů cookie:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Protokolování

Klienti vytvořeni prostřednictvím `IHttpClientFactory` zpráv protokolu záznamů pro všechny požadavky. Povolte příslušnou úroveň informací v konfiguraci protokolování a zostřikněte výchozí zprávy protokolu. Další protokolování, jako je například protokolování hlavičky požadavku, je zahrnuta pouze na úrovni trasování.

Kategorie protokolu použitá pro každého klienta obsahuje název klienta. Klient s názvem *MyNamedClient*, například protokoluje `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`zprávy s kategorií . Zprávy s upevněné s *LogicalHandler* dojít mimo kanál obslužné rutiny požadavku. Na žádost jsou zprávy zaznamenány dříve, než všechny ostatní obslužné rutiny v kanálu zpracovaly. Na odpověď zprávy jsou zaznamenány po všechny ostatní obslužné rutiny kanálu obdrželi odpověď.

Protokolování také dochází uvnitř kanálu obslužné rutiny požadavku. V příkladu *MyNamedClient* jsou tyto zprávy zaznamenány `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`proti kategorii protokolu . U požadavku k tomu dochází po spuštění všech ostatních obslužných rutin a bezprostředně před odesláním požadavku v síti. Na odpověď toto protokolování zahrnuje stav odpovědi před průchodem zpět prostřednictvím kanálu obslužné rutiny.

Povolení protokolování mimo a uvnitř kanálu umožňuje kontrolu změn provedených jinými obslužnými rutinami kanálu. To může zahrnovat například změny hlaviček požadavků nebo stavového kódu odpovědi.

Zahrnutí názvu klienta v kategorii protokolu umožňuje filtrování protokolů pro konkrétní pojmenované klienty v případě potřeby.

## <a name="configure-the-httpmessagehandler"></a>Konfigurace httpmessagehandleru

Může být nutné řídit konfiguraci `HttpMessageHandler` vnitřní používá klienta.

Při `IHttpClientBuilder` přidávání pojmenovaných nebo zadali klientů je vrácena vrácena. Metoda <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> rozšíření lze definovat delegáta. Delegát se používá k vytvoření `HttpMessageHandler` a konfiguraci primární používané tímto klientem:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Použití ihttpclientfactory v konzolové aplikaci

V konzolové aplikaci přidejte do projektu následující odkazy na balíček:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

V následujícím příkladu:

* <xref:System.Net.Http.IHttpClientFactory>je registrován v kontejneru služeb [obecného hostitele.](xref:fundamentals/host/generic-host)
* `MyService`vytvoří instanci továrny klienta ze služby, která se používá k vytvoření . `HttpClient` `HttpClient`se používá k načtení webové stránky.
* `Main`vytvoří obor pro spuštění metody `GetPage` služby a zápis prvních 500 znaků obsahu webové stránky do konzoly.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Middleware šíření záhlaví

Šíření záhlaví je komunitou podporovaný middleware k šíření hlaviček HTTP z příchozího požadavku na odchozí požadavky klienta HTTP. Použití šíření záhlaví:

* Odkaz na komunitu podporovaný port balíčku [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 a novější podporuje [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).

* Konfigurace middlewaru `HttpClient` `Startup`a v :

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* Klient obsahuje nakonfigurované hlavičky pro odchozí požadavky:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Další zdroje

* [Použití HttpClientFactory k implementaci odolných požadavků HTTP](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementace opakovaných pokusů o volání HTTP s exponenciálním vypnutím pomocí zásad HttpClientFactory a Polly](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementace systému jističe](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
