---
title: Konfigurace signalizace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat aplikace pro signalizaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246494"
---
# <a name="aspnet-core-signalr-configuration"></a>Konfigurace signalizace ASP.NET Core

## <a name="jsonmessagepack-serialization-options"></a>Možnosti serializace JSON/MessagePack

ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html). Každý protokol má možnosti konfigurace serializace.

::: moniker range=">= aspnetcore-3.0"

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol` lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`. Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`. Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je objekt <xref:System.Text.Json.JsonSerializerOptions> `System.Text.Json`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).

Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

V klientovi .NET existuje stejná rozšiřující metoda `AddJsonProtocol` v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Pro vyřešení metody rozšíření je nutné importovat obor názvů `Microsoft.Extensions.DependencyInjection`:

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

### <a name="switch-to-newtonsoftjson"></a>Přepnout na Newtonsoft. JSON

Pokud potřebujete funkce `Newtonsoft.Json`, které nejsou podporované v `System.Text.Json`, přečtěte si téma [Přepnutí na Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do metody `Startup.ConfigureServices`. Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`. Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot. Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" místo výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

V klientovi .NET existuje stejná rozšiřující metoda `AddJsonProtocol` v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Pro vyřešení metody rozšíření je nutné importovat obor názvů `Microsoft.Extensions.DependencyInjection`:

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

::: moniker-end

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.

### <a name="messagepack-serialization-options"></a>Možnosti serializace MessagePack

Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Následující tabulka obsahuje popis možností konfigurace Center pro signály:

::: moniker range=">= aspnetcore-3.0"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi. Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |
| `StreamBufferCapacity` | `10` | Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta. Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.|
| `MaximumReceiveMessageSize` | 32 KB | Maximální velikost jedné příchozí zprávy centra |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 sekund | Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive). Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno. Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.|
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi. Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 sekund | Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené. Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi. Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.  |
| `SupportedProtocols` | Všechny nainstalované protokoly | Protokoly podporované tímto rozbočovačem Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly. |
| `EnableDetailedErrors` | `false` | Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače. Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace. |

::: moniker-end

Možnosti lze nakonfigurovat pro všechna centra tím, že zadáte delegáty možností pro volání `AddSignalR` v `Startup.ConfigureServices`.

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

Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Rozšířené možnosti konfigurace protokolu HTTP

::: moniker range=">= aspnetcore-3.0"

Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta do [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti. Tyto možnosti jsou nakonfigurovány předáním delegáta do [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.

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

::: moniker-end

Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:

::: moniker range=">= aspnetcore-3.0"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku. Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti. |
| `AuthorizationData` | Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku. Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu hodnot `HttpTransportType`, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `AuthorizationData` | Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra | Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru |
| `TransportMaxBufferSize` | 32 KB | Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru. Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `Transports` | Všechny přenosy jsou povolené. | Bitové příznaky výčtu hodnot `HttpTransportType`, které mohou omezit přenos, který může klient použít pro připojení. |
| `LongPolling` | Viz níže. | Další možnosti specifické pro přenos dlouhého cyklického dotazování. |
| `WebSockets` | Viz níže. | Další možnosti specifické pro přenos pomocí protokolu WebSockets |

::: moniker-end

Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 sekund | Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování. Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji. |

Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 sekund | Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí. |
| `SubProtocolSelector` | `null` | Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu. Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota. |

## <a name="configure-client-options"></a>Konfigurace možností klienta

Možnosti klienta lze nakonfigurovat u typu @no__t 0 (k dispozici v klientech rozhraní .NET a v jazyce JavaScript). Je také k dispozici v klientovi Java, ale podtřídou `HttpHubConnectionBuilder` je to, co obsahuje možnosti konfigurace tvůrce a také na samotné `HubConnection`.

### <a name="configure-logging"></a>Konfigurovat protokolování

Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`. Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru. Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky. Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.

Chcete-li například povolit protokolování konzoly, nainstalujte balíček NuGet `Microsoft.Extensions.Logging.Console`. Zavolejte metodu rozšíření `AddConsole`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

V klientovi jazyka JavaScript existuje podobná metoda `configureLogging`. Zadejte hodnotu `LogLevel` určující minimální úroveň zpráv protokolu, které mají být vyprodukovány. Protokoly se zapisují do okna konzoly prohlížeče.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Místo hodnoty @no__t 0 můžete také zadat hodnotu `string` reprezentující název úrovně protokolu. To je užitečné při konfiguraci protokolování signalizace v prostředích, kde nemáte přístup k konstantám `LogLevel`.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

V následující tabulce jsou uvedeny dostupné úrovně protokolu. Hodnota, kterou zadáte pro `configureLogging` nastaví **minimální** úroveň protokolu, která bude protokolována. Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.

| Řetězec                      | logLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **nebo** `information` | `LogLevel.Information` |
| `warn` **nebo** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> Pokud chcete protokolování zcela zakázat, zadejte v metodě `configureLogging` `signalR.LogLevel.None`.

Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).

Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování. Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí. Následující fragment kódu ukazuje, jak použít `java.util.logging` u klienta signalizace v jazyce Java.

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

Transporty používané signálem lze nakonfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu). Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů. Ve výchozím nastavení jsou povolené všechny přenosy.

Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` u objektu Options, který je k dispozici pro `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

V klientovi Java se přenos vybere s metodou `withTransport` na `HttpHubConnectionBuilder`. Klient Java standardně používá přenos pomocí protokolu WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Klient Java Signale ještě nepodporuje záložní přenos.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Konfigurace ověřování nosiče

K poskytnutí ověřovacích dat spolu s požadavky na signalizaci použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token. V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím hlavičky `Authorization` s typem `Bearer`). V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets). V těchto případech je přístupový token poskytnutý jako hodnota řetězce dotazu `access_token`.

V klientovi .NET je možné zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:

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

V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) použijte k poskytnutí [RxJava](https://github.com/ReactiveX/RxJava) [jednoho no__t-3String >](https://reactivex.io/documentation/single.html). Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurace možností timeout a Keep-Alive

Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na samotném objektu @no__t 0:


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 sekund | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený. |

V klientovi .NET jsou hodnoty časového limitu zadány jako hodnoty `TimeSpan`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |
| `keepAliveIntervalInMilliseconds` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 sekund (15 000 milisekund) | Určuje interval, ve kterém klient odesílá zprávy nástroje test. Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu. Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu). Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |
| `HandshakeTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu). Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

V klientovi .NET jsou hodnoty časového limitu zadány jako hodnoty `TimeSpan`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 sekund (30 000 milisekund) | Vypršel časový limit aktivity serveru. Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`. Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu. Doporučená hodnota je číslo minimálně dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času. |
| `withHandshakeResponseTimeout` | 15 sekund | Vypršel časový limit počáteční metody handshake serveru. Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`. Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě. Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

::: moniker-end

---

### <a name="configure-additional-options"></a>Konfigurovat další možnosti

Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` v JavaScriptu) na `HubConnectionBuilder` nebo v různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` v klientovi Java:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Možnost .NET |  Výchozí hodnota | Popis |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `SkipNegotiation` | `false` | Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby signalizace Azure. |
| `ClientCertificates` | Obsahovat | Kolekce certifikátů TLS pro odeslání na požadavky na ověření. |
| `Cookies` | Obsahovat | Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP |
| `Credentials` | Obsahovat | Přihlašovací údaje, které se mají poslat s každou žádostí HTTP |
| `CloseTimeout` | 5 sekund | Jenom objekty WebSockets. Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření. Pokud server nepotvrdí zavření v této době, klient se odpojí. |
| `Headers` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |
| `HttpMessageHandlerFactory` | `null` | Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odeslání požadavků HTTP. Nepoužívá se pro připojení pomocí protokolu WebSocket. Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr. Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`. **Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.** |
| `Proxy` | `null` | Proxy server HTTP, který se má použít při odesílání požadavků HTTP. |
| `UseDefaultCredentials` | `false` | Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets. To umožňuje použití ověřování systému Windows. |
| `WebSocketConfiguration` | `null` | Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket. Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Možnost JavaScriptu | Výchozí hodnota | Popis |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `skipNegotiation` | `false` | Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby signalizace Azure. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Možnost jazyka Java | Výchozí hodnota | Popis |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP. |
| `shouldSkipNegotiate` | `false` | Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání. **Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**. Toto nastavení se nedá povolit při použití služby signalizace Azure. |
| `withHeader` `withHeaders` | Obsahovat | Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP |

---

V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Další zdroje informací:

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
