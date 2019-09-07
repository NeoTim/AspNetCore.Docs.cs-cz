---
title: Rozdíly mezi signálem a signálem ASP.NET Core
author: bradygaster
description: Rozdíly mezi signálem a signálem ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746541"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Rozdíly mezi signálem ASP.NET a signálem ASP.NET Core

Signál ASP.NET Core není kompatibilní s klienty nebo servery pro signál ASP.NET. Tento článek obsahuje podrobnosti o funkcích, které byly v ASP.NET Coreovém signalizaci odebrány nebo změněny.

## <a name="how-to-identify-the-signalr-version"></a>Jak identifikovat verzi signalizace

|                      | ASP.NET SignalR | ASP.NET Core signál |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore. Signal](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Klientské balíčky NuGet | [Microsoft. AspNet. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Balíček npm klienta | [SignalR](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) zastaralé  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | ASP.NET Core |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Framework 4.6.1 nebo novější<br>.NET Core 2,1 nebo novější |

## <a name="feature-differences"></a>Rozdíly ve funkcích

### <a name="automatic-reconnects"></a>Automatické opětovné připojení

Automatické opětovné připojení není v nástroji ASP.NET Core signalizace podporováno. Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, pokud se chce znovu připojit. V nástroji ASP.NET Signal se signál pokusí znovu připojit k serveru, pokud je připojení vyřazené.

### <a name="protocol-support"></a>Podpora protokolů

ASP.NET Core Signal podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol). Navíc můžete vytvářet vlastní protokoly.

### <a name="transports"></a>Přenosy

V nástroji ASP.NET Core Signal není podporován přenos snímků typu navždy.

## <a name="differences-on-the-server"></a>Rozdíly na serveru

Knihovny na straně serveru ASP.NET Core signalizace jsou součástí balíčku [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , který je součástí šablony **webové aplikace ASP.NET Core** pro Razor i projekty MVC.

ASP.NET Core Signaler je ASP.NET Core middleware, takže se musí nakonfigurovat voláním [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat směrování, mapujte trasy k rozbočovačům uvnitř volání metody [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) v `Startup.Configure` metodě.


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Chcete-li konfigurovat směrování, mapujte trasy k rozbočovačům uvnitř volání metody [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) v `Startup.Configure` metodě.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Rychlé relace

Model horizontálního navýšení kapacity pro signál ASP.NET umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě. V ASP.NET Core Signal musí klient komunikovat se stejným serverem po dobu trvání připojení. Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace. Pro škálování pomocí [služby signalizace Azure](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.

### <a name="single-hub-per-connection"></a>Jedno centrum na připojení

V nástroji ASP.NET Core Signal byl model připojení zjednodušený. Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.

### <a name="streaming"></a>Streamování

ASP.NET Core Signal teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.

### <a name="state"></a>Stav

Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným jako HubState) a také podporovat zprávy o průběhu. V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.

### <a name="persistentconnection-removal"></a>Odebrání PersistentConnection

V nástroji ASP.NET Core Signal byla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .

### <a name="globalhost"></a>GlobalHost

ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní. Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di. Objekt, který se používá v nástroji ASP.NET Signal k `HubContext` získání neexistence v signálu ASP.NET Core. `GlobalHost`

### <a name="hubpipeline"></a>HubPipeline

Signál ASP.NET Core nemá podporu pro `HubPipeline` moduly.

## <a name="differences-on-the-client"></a>Rozdíly na klientovi

### <a name="typescript"></a>TypeScript

Klient signalizace ASP.NET Core je napsán v [TypeScript](https://www.typescriptlang.org/). Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScriptový klient je hostovaný na [npm](https://www.npmjs.com/)

V předchozích verzích byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. Pro základní verze [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) obsahuje balíček npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . Použijte npm k získání a instalaci `@aspnet/signalr` balíčku npm.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.

### <a name="internet-explorer-support"></a>Podpora aplikace Internet Explorer

ASP.NET Core Signal vyžaduje aplikaci Microsoft Internet Explorer 11 nebo novější (ASP.NET Signal podporuje Microsoft Internet Explorer 8 a novější).

### <a name="javascript-client-method-syntax"></a>Syntaxe metody klienta jazyka JavaScript

Syntaxe jazyka JavaScript se změnila z předchozí verze nástroje Signal. Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Použijte metodu [on](/javascript/api/@aspnet/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

Po vytvoření metody klienta spusťte připojení k rozbočovači. Řetězení metody [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pro protokolování a zpracování chyb.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>Proxy servery centra

Proxy servery centra se už negenerují automaticky. Místo toho je název metody předán do [vyvolání](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.

### <a name="net-and-other-clients"></a>.NET a další klienti

Balíček `Microsoft.AspNetCore.SignalR.Client` NuGet obsahuje klientské knihovny .NET pro signál ASP.NET Core.

Pomocí [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) můžete vytvořit a sestavit instanci připojení k rozbočovači.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Rozdíly ve škálování

Signál ASP.NET podporuje SQL Server a Redis. ASP.NET Core Signal podporuje službu a Redis služby Azure Signal.

### <a name="aspnet"></a>ASP.NET

* [Škálování signálu pomocí Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Škálování signálu pomocí Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [Škálování signálu pomocí SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Služba Azure SignalR](/azure/azure-signalr/)
* [Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Podporované platformy](xref:signalr/supported-platforms)
