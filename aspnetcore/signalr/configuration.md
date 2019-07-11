---
title: Konfigurace jádra SignalR technologie ASP.NET
author: bradygaster
description: Zjistěte, jak nakonfigurovat aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813454"
---
# <a name="aspnet-core-signalr-configuration"></a>Konfigurace jádra SignalR technologie ASP.NET

## <a name="jsonmessagepack-serialization-options"></a>Serializace JSON/MessagePack možnosti

Funkce SignalR technologie ASP.NET Core podporuje dva protokoly pro kódování zprávy: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Všechny protokoly, které obsahuje možnosti konfigurace serializace.

Serializace JSON lze nastavit na serveru pomocí [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozšíření, které mohou být přidány po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v vaše `Startup.ConfigureServices` metody. `AddJsonProtocol` Metoda přijímá delegát, který přijímá `options` objektu. [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) vlastnost k tomuto objektu je JSON.NET `JsonSerializerSettings` objekt, který můžete použít ke konfiguraci serializace argumenty a návratové hodnoty. Najdete v článku [JSON.NET dokumentaci](https://www.newtonsoft.com/json/help/html/Introduction.htm) další podrobnosti.

Jako příklad konfigurace serializátoru, který chcete použít místo výchozí názvy "camelCase", "PascalCase" názvy vlastností, pomocí následujícího kódu:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

V klientovi .NET, stejné `AddJsonProtocol` – metoda rozšíření existuje v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován do resolve – metoda rozšíření:

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
> Není možné konfigurovat pomocí serializace JSON klienta jazyka JavaScript v tuto chvíli.

### <a name="messagepack-serialization-options"></a>MessagePack možnosti serializace

Poskytnutím delegáta, kterého lze nakonfigurovat MessagePack serializace [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) volání. Zobrazit [MessagePack v knihovně SignalR](xref:signalr/messagepackhubprotocol) další podrobnosti.

> [!NOTE]
> Není možné konfigurovat MessagePack serializace pomocí jazyka JavaScript klienta v tuto chvíli.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti pro konfiguraci rozbočovače SignalR:

::: moniker range=">= aspnetcore-3.0"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že klient odpojen Pokud neobdržel zprávu (včetně udržování) v tomto intervalu. Může to trvat déle než tento interval časového limitu klienta ve skutečnosti označit odpojené kvůli jak tato možnost je implementovaná. Doporučená hodnota je double `KeepAliveInterval` hodnotu.|
| `HandshakeTimeout` | 15 sekund | Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno. Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí. Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného. Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta. Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokolů podporovaných toto centrum. Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače. Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které mohou být ukládány do vyrovnávací paměti pro klienta nahrání datových proudů. Při dosažení tohoto limitu zpracování volání blokovaný, dokud se server zpracuje položek datového proudu.|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že klient odpojen Pokud neobdržel zprávu (včetně udržování) v tomto intervalu. Může to trvat déle než tento interval časového limitu klienta ve skutečnosti označit odpojené kvůli jak tato možnost je implementovaná. Doporučená hodnota je double `KeepAliveInterval` hodnotu.|
| `HandshakeTimeout` | 15 sekund | Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno. Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí. Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného. Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta. Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokolů podporovaných toto centrum. Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače. Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno. Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí. Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného. Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta. Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokolů podporovaných toto centrum. Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače. Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje. |

::: moniker-end

Možnosti je možné nakonfigurovat pro všechna centra poskytnutím delegáta možnosti k `AddSignalR` volání v `Startup.ConfigureServices`.

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

Možnosti pro jedno centrum přepsat globální možnosti uvedené v `AddSignalR` a dá se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Použití `HttpConnectionDispatcherOptions` konfigurace upřesňujících nastavení související s přenosy a správa vyrovnávací paměti. Tyto možnosti jsou nakonfigurované pomocí předání delegáta, kterého [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.

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

Následující tabulka popisuje možnosti pro konfiguraci rozšířené možnosti HTTP SignalR technologie ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB. | Maximální počet bajtů přijatých z klienta, který vyrovnávací paměti serveru. Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti. |
| `AuthorizationData` | Data jsou automaticky shromážděna z `Authorize` atributy použité u třídy rozbočovače. | Seznam [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objekty sloužící k určení, pokud je klient autorizaci k připojení k rozbočovači. |
| `TransportMaxBufferSize` | 32 KB. | Maximální počet bajtů odeslaných aplikace, která vyrovnávací paměti serveru. Zvýšení hodnoty tuto umožňuje serveru odesílat větší zprávy, ale může mít negativní vliv na využití paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitová maska z `HttpTransportType` hodnoty, které můžete omezit přenosy klienta můžete použít pro připojení. |
| `LongPolling` | Níže jsou uvedeny. | Další možnosti specifické pro dlouhé dotazování přenosu. |
| `WebSockets` | Níže jsou uvedeny. | Další možnosti specifické pro přenos objekty Websocket. |

Dlouhý interval dotazování přenosu má další možnosti, které lze konfigurovat pomocí `LongPolling` vlastnost:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální množství času na server čeká na zprávu k odeslání do klienta před ukončením žádosti o jednotné hlasování. Snížení hodnoty způsobí, že klient k vydávání nových žádostí o dotazování častěji. |

Přenos pomocí protokolu WebSocket má další možnosti, které lze konfigurovat pomocí `WebSockets` vlastnost:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Po zavření serveru, pokud se klientovi nepodaří zavřete v tomto časovém intervalu, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který je možné nastavit `Sec-WebSocket-Protocol` vlastní hodnoty záhlaví. Delegát přijme hodnoty vyžádané klientem jako vstup a měl by vrátit na požadovanou hodnotu. |

## <a name="configure-client-options"></a>Konfigurovat možnosti klienta

Možnosti klienta lze nakonfigurovat podle `HubConnectionBuilder` typ (k dispozici v klientech .NET a JavaScript). Je také k dispozici v jazyce Java pro klienta, ale `HttpHubConnectionBuilder` podtřídy je, co obsahuje Tvůrce možnosti konfigurace, stejně jako na `HubConnection` samotný.

### <a name="configure-logging"></a>Konfigurace protokolování

Protokolování je nakonfigurován v klientu .NET pomocí `ConfigureLogging` metody. Protokolování poskytovatelů a filtry můžete zaregistrovat stejně, jako jsou na serveru. Zobrazit [protokolování v ASP.NET Core](xref:fundamentals/logging/index) Další informace naleznete v dokumentaci.

> [!NOTE]
> Aby bylo možné zaregistrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Najdete v článku [vestavěné protokolování poskytovatelé](xref:fundamentals/logging/index#built-in-logging-providers) část dokumentace pro úplný seznam.

Například pokud chcete povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Volání `AddConsole` – metoda rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi JavaScript podobný `configureLogging` metody existuje. Zadejte `LogLevel` hodnotu, která minimální úroveň zprávy protokolu k vytvoření. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Místo `LogLevel` hodnoty, můžete zadat taky `string` hodnotu představující název úrovně protokolu. To je užitečné při konfiguraci SignalR protokolování v prostředí, ve kterém nemáte přístup k `LogLevel` konstanty.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Následující tabulka uvádí dostupné protokolu úrovně. Hodnota poskytnete `configureLogging` nastaví **minimální** úrovně, která se zaznamenala protokolování. Zprávy zaprotokolované na této úrovni **nebo úrovně uvedených za něj v tabulce**, budou protokolovány.

| odkazy řetězců | LogLevel |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| `"info"` **Nebo** `"information"` | `LogLevel.Information` |
| `"warn"` **Nebo** `"warning"` | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> Chcete-li zakázat protokolování úplně, zadejte `signalR.LogLevel.None` v `configureLogging` metody.

Další informace o protokolování, najdete v článku [dokumentace k nástroji Diagnostika SignalR](xref:signalr/diagnostics).

Používá klientskou sadou SignalR Java [SLF4J](https://www.slf4j.org/) knihovny pro protokolování. Jde o rozhraní API vysoké úrovně protokolování, který umožňuje uživatelům knihovny zvolili vlastní implementace protokolování konkrétní díky možnostem protokolování konkrétní závislost. Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientskou sadou SignalR Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud nenakonfigurujete přihlášení závislostí, načte SLF4J protokolovacího nástroje výchozí žádné operace s tímto upozorněním:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To můžete bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Nakonfigurovat povolené přenosy

Přenosy systém signalr se dá nakonfigurovat v `WithUrl` volání (`withUrl` v JavaScriptu). Bitový OR hodnoty `HttpTransportType` slouží k omezení klienta používat pouze zadaný přenosy. Ve výchozím nastavení jsou povolené všechny přenosy.

Například zakázat přenos Server-Sent události, ale povolte připojení objekty Websocket a dlouhý interval dotazování:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientovi JavaScript přenosy jsou nakonfigurovány tak, že nastavíte `transport` na objekt možnosti poskytnuté `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

V této verzi jazyka Java klienta websocket je k dispozici pouze přenosu.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Klientskou sadou Java, přenos je vybraná s `withTransport` metodu `HttpHubConnectionBuilder`. Klientskou sadou Java ve výchozím nastavení používá přenos objekty Websocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klientskou sadou SignalR Java přenosu záložní ještě nepodporuje.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

Pokud chcete poskytnout ověřovací data spolu s knihovnou SignalR požadavky, použijte `AccessTokenProvider` možnost (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET Tento přístupový token předaný jako HTTP "Ověřování nosiče" token (použití `Authorization` záhlaví s typem `Bearer`). V klientovi JavaScript přístupový token se používá jako nosný token **s výjimkou** v několika případech, kdy prohlížeč rozhraní API omezit schopnosti uplatňovat hlavičky (konkrétně v Server-Sent události a protokoly Websocket požadavky). V těchto případech je přístupový token k dispozici jako hodnotu řetězce dotazu `access_token`.

V klientovi .NET `AccessTokenProvider` možnost lze zadat pomocí možnosti delegáta v `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScript přístupový token je nakonfigurovaný tak, že nastavíte `accessTokenFactory` na objekt možnosti v `withUrl`:

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

V klientovi SignalR Java můžete nakonfigurovat nosný token pro účely ověření zadáním tokenu továrnu přístup ke [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Použití [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytování [RxJava](https://github.com/ReactiveX/RxJava) [jeden\<řetězec >](https://reactivex.io/documentation/single.html). Voláním [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžou psát logiku k vytvoření přístupové tokeny pro vašeho klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace časového limitu a udržování možnosti

Další možnosti pro konfiguraci časového limitu a zachování chování, které jsou k dispozici na `HubConnection` samotného objektu:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Časový limit pro aktivity serveru. Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `Closed` událostí (`onclose` v JavaScriptu). Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu. Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí. |
| `HandshakeTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server není v tomto intervalu odeslání odpovědi handshake, klient zruší handshake a aktivační události `Closed` událostí (`onclose` v JavaScriptu). Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí. Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

V klientovi .NET jsou zadané hodnoty časového limitu jako `TimeSpan` hodnoty.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Časový limit pro aktivity serveru. Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `onclose` událostí. Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu. Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
|`getServerTimeout``setServerTimeout` | 30 sekund (30 000 milisekund) | Časový limit pro aktivity serveru. Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `onClose` událostí. Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu. Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí. |
| `withHandshakeResponseTimeout` | 15 sekund | Časový limit pro počáteční server handshake. Pokud server není v tomto intervalu odeslání odpovědi handshake, klient zruší handshake a aktivační události `onClose` událostí. Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí. Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti se dá nakonfigurovat v `WithUrl` (`withUrl` v JavaScriptu) metodu na `HubConnectionBuilder` nebo v různých rozhraní API pro konfiguraci na `HttpHubConnectionBuilder` v klientskou sadou Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| .NET – možnost |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte na `true` přeskočit krok vyjednávání. **Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**. Toto nastavení není možné při použití služby Azure SignalR. |
| `ClientCertificates` | prázdný | Kolekce certifikáty TLS odeslat k ověření požadavků. |
| `Cookies` | prázdný | Kolekce souborů cookie protokolu HTTP k odeslání při každé žádosti protokolu HTTP. |
| `Credentials` | prázdný | Přihlašovací údaje pro odesílání při každé žádosti protokolu HTTP. |
| `CloseTimeout` | 5 sekund | Pouze objekty Websocket. Maximální množství času, klient počká po uzavření pro server a potvrďte žádosti o uzavření. Pokud server není v tuto chvíli Potvrdit uzavření, klient neodpojí. |
| `Headers` | prázdný | Mapa dodatečné hlavičky protokolu HTTP k odeslání při každé žádosti protokolu HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahradit `HttpMessageHandler` používaný k odesílání požadavků HTTP. Není možné použít u připojení pomocí protokolu WebSocket. Tento delegát musí vrátit nenulovou hodnotu, a přijímá jako parametr výchozí hodnotu. Upravte nastavení podle této výchozí hodnoty a vrácení nebo vrátí novou `HttpMessageHandler` instance. **Při nahrazování obslužnou rutinu nezapomeňte si zkopírovat na nastavení, která chcete zachovat od zadané obslužné rutiny, v opačném případě nakonfigurovaných možností (například soubory cookie a hlavičky) se nevztahuje na novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu k odeslání výchozí přihlašovací údaje pro požadavky HTTP a Websocket. To umožňuje použití ověřování Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který slouží k nakonfigurování dalších možností protokolu WebSocket. Obdrží instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , který lze použít ke konfiguraci možností. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Možnost jazyka JavaScript | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte na `true` přeskočit krok vyjednávání. **Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**. Toto nastavení není možné při použití služby Azure SignalR. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Možnost Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte na `true` přeskočit krok vyjednávání. **Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**. Toto nastavení není možné při použití služby Azure SignalR. |
| `withHeader``withHeaders` | prázdný | Mapa dodatečné hlavičky protokolu HTTP k odeslání při každé žádosti protokolu HTTP. |

---

V klientovi .NET tyto možnosti je možné upravovat prostřednictvím delegáta možnosti poskytnuté `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScript tyto možnosti lze zadat v objektu jazyka JavaScript poskytuje k `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientskou sadou Java, tyto možnosti v lze nakonfigurovat pomocí metod `HttpHubConnectionBuilder` vrácených `HubConnectionBuilder.create("HUB URL")`

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
