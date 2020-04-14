---
title: ASP.NET SignalR konfigurace jádra
author: bradygaster
description: Přečtěte si, SignalR jak nakonfigurovat aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228137"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET konfigurace Core SignalR

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace balíčku JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`. Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt. [Vlastnost PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) u tohoto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objektu je objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace naleznete v [dokumentaci System.Text.Json](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor tak, aby neměnil názvy vlastností, namísto výchozích názvů camelCase použijte následující kód v aplikaci `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.

### <a name="switch-to-newtonsoftjson"></a>Přejít na Newtonsoft.Json

Pokud potřebujete `Newtonsoft.Json` funkce, které nejsou `System.Text.Json`podporovány v , viz [Přepnout na Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Možnosti serializace messagepacku

Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.

## <a name="configure-server-options"></a>Konfigurace možností serveru

Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu. Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována. Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.|
| `HandshakeTimeout` | 15 sekund | Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené. Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto centrem. Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody. Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které mohou být uloženy do vyrovnávací paměti pro datové proudy pro odesílání klienta. Pokud je dosaženo tohoto limitu, zpracování vyvolání je blokován, dokud server zpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy rozbočovače. |

Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti před použitím protitlaku. Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy rychleji bez použití protitlaku, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub. | Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači. |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti před sledováním protitlaku. Zvýšení této hodnoty umožňuje serveru do vyrovnávací paměti větší zprávy rychleji bez čekání na protitlak, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povoleny. | Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhé dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos WebSockets. |
| `MinimumProtocolVersion` | 0 | Zadejte minimální verzi protokolu negotiate. Používá se k omezení klientů na novější verze. |

Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování. Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji. |

Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript). Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.

### <a name="configure-logging"></a>Konfigurace protokolování

Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody. Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru. Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)

> [!NOTE]
> Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.

Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet. Volání `AddConsole` metody rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi JavaScript `configureLogging` existuje podobná metoda. Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření. Protokoly jsou zapsány do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Namísto `LogLevel` hodnoty můžete také zadat `string` hodnotu představující název na úrovni protokolu. To je užitečné při konfiguraci protokolování SignalR v prostředích, kde nemáte přístup k `LogLevel` konstantám.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou `configureLogging` zadáte pro nastavení **minimální** úroveň protokolu, která bude zaznamenána. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou zaznamenány.

| Řetězec                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**nebo**`information` | `LogLevel.Information` |
| `warn`**nebo**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.

Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)

Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/) Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování. Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu). Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy. Všechny přenosy jsou ve výchozím nastavení povoleny.

Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi webových zásuvek klienta Java je pouze dostupný přenos.

V klientovi Java je přenos `withTransport` vybrán s `HttpHubConnectionBuilder`metodou na . Klient Java ve výchozím nastavení používá přenos WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klient Java SignalR ještě nepodporuje záložní přenos.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token. V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ). V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets). V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .

V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností časového limitu a udržování v životě

Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript). Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `HandshakeTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript). Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `withHandshakeResponseTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `ClientCertificates` | Prázdné | Kolekce certifikátů TLS k odeslání k ověření požadavků. |
| `Cookies` | Prázdné | Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `Credentials` | Prázdné | Pověření k odeslání s každým požadavkem HTTP. |
| `CloseTimeout` | 5 sekund | Pouze websockets. Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření. Pokud server nepotvrdí uzavření během této doby, klient se odpojí. |
| `Headers` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP. Nepoužívá se pro připojení WebSocket. Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr. Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci. **Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.** |
| `Proxy` | `null` | Proxy HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností WebSocket. Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Java možnost | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `withHeader` `withHeaders` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |

---

V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace balíčku JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`. Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt. [Vlastnost PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) u tohoto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objektu je objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace naleznete v [dokumentaci System.Text.Json](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor tak, aby neměnil názvy vlastností, namísto výchozích názvů camelCase použijte následující kód v aplikaci `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.

### <a name="switch-to-newtonsoftjson"></a>Přejít na Newtonsoft.Json

Pokud potřebujete `Newtonsoft.Json` funkce, které nejsou `System.Text.Json`podporovány v , viz [Přepnout na Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Možnosti serializace messagepacku

Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.

## <a name="configure-server-options"></a>Konfigurace možností serveru

Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu. Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována. Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.|
| `HandshakeTimeout` | 15 sekund | Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené. Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto centrem. Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody. Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které mohou být uloženy do vyrovnávací paměti pro datové proudy pro odesílání klienta. Pokud je dosaženo tohoto limitu, zpracování vyvolání je blokován, dokud server zpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy rozbočovače. |

Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti před použitím protitlaku. Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy rychleji bez použití protitlaku, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub. | Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači. |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti před sledováním protitlaku. Zvýšení této hodnoty umožňuje serveru do vyrovnávací paměti větší zprávy rychleji bez čekání na protitlak, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povoleny. | Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhé dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos WebSockets. |

Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování. Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji. |

Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript). Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.

### <a name="configure-logging"></a>Konfigurace protokolování

Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody. Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru. Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)

> [!NOTE]
> Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.

Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet. Volání `AddConsole` metody rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi JavaScript `configureLogging` existuje podobná metoda. Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření. Protokoly jsou zapsány do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Namísto `LogLevel` hodnoty můžete také zadat `string` hodnotu představující název na úrovni protokolu. To je užitečné při konfiguraci protokolování SignalR v prostředích, kde nemáte přístup k `LogLevel` konstantám.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou `configureLogging` zadáte pro nastavení **minimální** úroveň protokolu, která bude zaznamenána. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou zaznamenány.

| Řetězec                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**nebo**`information` | `LogLevel.Information` |
| `warn`**nebo**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.

Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)

Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/) Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování. Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu). Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy. Všechny přenosy jsou ve výchozím nastavení povoleny.

Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi webových zásuvek klienta Java je pouze dostupný přenos.

V klientovi Java je přenos `withTransport` vybrán s `HttpHubConnectionBuilder`metodou na . Klient Java ve výchozím nastavení používá přenos WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klient Java SignalR ještě nepodporuje záložní přenos.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token. V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ). V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets). V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .

V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností časového limitu a udržování v životě

Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript). Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `HandshakeTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript). Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `withHandshakeResponseTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `ClientCertificates` | Prázdné | Kolekce certifikátů TLS k odeslání k ověření požadavků. |
| `Cookies` | Prázdné | Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `Credentials` | Prázdné | Pověření k odeslání s každým požadavkem HTTP. |
| `CloseTimeout` | 5 sekund | Pouze websockets. Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření. Pokud server nepotvrdí uzavření během této doby, klient se odpojí. |
| `Headers` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP. Nepoužívá se pro připojení WebSocket. Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr. Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci. **Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.** |
| `Proxy` | `null` | Proxy HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností WebSocket. Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Java možnost | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `withHeader` `withHeaders` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |

---

V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace balíčku JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě. Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt. [Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.

### <a name="messagepack-serialization-options"></a>Možnosti serializace messagepacku

Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.

## <a name="configure-server-options"></a>Konfigurace možností serveru

Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu. Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována. Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.|
| `HandshakeTimeout` | 15 sekund | Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené. Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto centrem. Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody. Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |

Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti. Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| `AuthorizationData` | Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub. | Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači. |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti. Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povoleny. | Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhé dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos WebSockets. |

Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování. Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji. |

Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript). Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.

### <a name="configure-logging"></a>Konfigurace protokolování

Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody. Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru. Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)

> [!NOTE]
> Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.

Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet. Volání `AddConsole` metody rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi JavaScript `configureLogging` existuje podobná metoda. Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření. Protokoly jsou zapsány do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.

Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)

Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/) Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování. Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu). Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy. Všechny přenosy jsou ve výchozím nastavení povoleny.

Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi webových zásuvek klienta Java je pouze dostupný přenos.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token. V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ). V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets). V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .

V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností časového limitu a udržování v životě

Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript). Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `HandshakeTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript). Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `withHandshakeResponseTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy ping. Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `ClientCertificates` | Prázdné | Kolekce certifikátů TLS k odeslání k ověření požadavků. |
| `Cookies` | Prázdné | Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `Credentials` | Prázdné | Pověření k odeslání s každým požadavkem HTTP. |
| `CloseTimeout` | 5 sekund | Pouze websockets. Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření. Pokud server nepotvrdí uzavření během této doby, klient se odpojí. |
| `Headers` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP. Nepoužívá se pro připojení WebSocket. Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr. Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci. **Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.** |
| `Proxy` | `null` | Proxy HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností WebSocket. Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Java možnost | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `withHeader` `withHeaders` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |

---

V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace balíčku JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě. Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt. [Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.

### <a name="messagepack-serialization-options"></a>Možnosti serializace messagepacku

Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.

## <a name="configure-server-options"></a>Konfigurace možností serveru

Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené. Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto centrem. Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody. Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |

Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti. Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| `AuthorizationData` | Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub. | Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači. |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti. Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povoleny. | Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhé dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos WebSockets. |

Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování. Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji. |

Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript). Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.

### <a name="configure-logging"></a>Konfigurace protokolování

Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody. Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru. Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)

> [!NOTE]
> Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.

Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet. Volání `AddConsole` metody rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi JavaScript `configureLogging` existuje podobná metoda. Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření. Protokoly jsou zapsány do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.

Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)

Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/) Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování. Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu). Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy. Všechny přenosy jsou ve výchozím nastavení povoleny.

Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token. V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ). V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets). V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .

V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java) Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html). S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností časového limitu a udržování v životě

Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript). Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |
| `HandshakeTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript). Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Časový čas pro aktivitu serveru. Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost. Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu. Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru, aby čas pro příkaz ping k doručení. |
| `withHandshakeResponseTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost. Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě. Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `ClientCertificates` | Prázdné | Kolekce certifikátů TLS k odeslání k ověření požadavků. |
| `Cookies` | Prázdné | Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `Credentials` | Prázdné | Pověření k odeslání s každým požadavkem HTTP. |
| `CloseTimeout` | 5 sekund | Pouze websockets. Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření. Pokud server nepotvrdí uzavření během této doby, klient se odpojí. |
| `Headers` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP. Nepoužívá se pro připojení WebSocket. Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr. Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci. **Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.** |
| `Proxy` | `null` | Proxy HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností WebSocket. Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Java možnost | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte tuto `true` na přeskočení kroku vyjednávání. **Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**. Toto nastavení nelze povolit při použití služby Azure SignalR. |
| `withHeader` `withHeaders` | Prázdné | Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP. |

---

V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
