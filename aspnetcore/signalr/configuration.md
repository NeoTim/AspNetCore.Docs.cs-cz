---
title: SignalRKonfigurace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 809bdc777b6307314a7bcde82ab5e0c6888db99b
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074465"
---
# <a name="aspnet-core-signalr-configuration"></a>SignalRKonfigurace ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` . `AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt. Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:

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
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Přepnout na Newtonsoft.Js

Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti konfigurace SignalR Center:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta. Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy centra |

Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku. Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra. | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku. Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |
| `MinimumProtocolVersion` | 0 | Zadejte minimální verzi protokolu Negotiate. Slouží k omezení klientů na novější verze. |

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Zavolejte `AddConsole` metodu rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda. Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu. To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.

| Řetězec                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **nebo** `information` | `LogLevel.Information` |
| `warn` **nebo** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.

V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` . Klient Java standardně používá přenos pomocí protokolu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java ještě nepodporuje přenosovou zálohu.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .

V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `headers` | `null` | Slovník hlaviček odeslaných s každou žádostí HTTP Odesílání hlaviček v prohlížeči nefunguje pro objekty WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream. |
| `logMessageContent` | `null` | Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem. |
| `skipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withCredentials` | `true` | Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS. Azure App Service používá pro rychlé relace soubory cookie a vyžaduje, aby tato možnost fungovala správně. Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` . `AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt. Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:

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
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Přepnout na Newtonsoft.Js

Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti konfigurace SignalR Center:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta. Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy centra |

Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku. Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra. | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku. Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |
| `MinimumProtocolVersion` | 0 | Zadejte minimální verzi protokolu Negotiate. Slouží k omezení klientů na novější verze. |

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Zavolejte `AddConsole` metodu rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda. Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu. To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.

| Řetězec                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **nebo** `information` | `LogLevel.Information` |
| `warn` **nebo** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.

V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` . Klient Java standardně používá přenos pomocí protokolu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java ještě nepodporuje přenosovou zálohu.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .

V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `logMessageContent` | `null` | Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem. |
| `skipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` . `AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt. Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:

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
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Přepnout na Newtonsoft.Js

Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti konfigurace SignalR Center:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta. Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy centra |

Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku. Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra. | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku. Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Zavolejte `AddConsole` metodu rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda. Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu. To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.

| Řetězec                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **nebo** `information` | `LogLevel.Information` |
| `warn` **nebo** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.

V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` . Klient Java standardně používá přenos pomocí protokolu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRKlient Java ještě nepodporuje přenosovou zálohu.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .

V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `logMessageContent` | `null` | Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem. |
| `skipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě. `AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt. Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:

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
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti konfigurace SignalR Center:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace. |

Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `AuthorizationData` | Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra. | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Zavolejte `AddConsole` metodu rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda. Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .

V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený. |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `logMessageContent` | `null` | Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem. |
| `skipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě. `AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt. Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:

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
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka popisuje možnosti konfigurace SignalR Center:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi. Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace. |

Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `AuthorizationData` | Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra. | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet. Zavolejte `AddConsole` metodu rozšíření:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda. Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.

Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).

SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) . Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Tuto možnost lze bezpečně ignorovat.

### <a name="configure-allowed-transports"></a>Konfigurace povolených přenosů

Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .

V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času. |

# <a name="java"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurace dalších možností

Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `logMessageContent` | `null` | Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem. |
| `skipNegotiation` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavením tohoto `true` postupu přeskočíte krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby Azure SignalR . |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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
