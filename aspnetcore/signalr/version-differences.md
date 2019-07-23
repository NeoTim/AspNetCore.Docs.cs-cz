---
title: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: d140becaed5b4fc765b7a72571c7f5dce7f4e3d4
ms.sourcegitcommit: 28646e8ca62fb094db1557b5c0c02d5b45531824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2019
ms.locfileid: "67333449"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Rozdíly mezi funkce SignalR technologie ASP.NET a technologie SignalR technologie ASP.NET Core

Funkce SignalR technologie ASP.NET Core není kompatibilní s klientů nebo serverů pro funkci SignalR technologie ASP.NET. Tento článek podrobně popisuje funkce, které byly odstraněny nebo změněny v knihovně SignalR technologie ASP.NET Core.

## <a name="how-to-identify-the-signalr-version"></a>Jak určit verzi SignalR

|                      | ASP.NET SignalR | Funkce SignalR technologie ASP.NET Core |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet server | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Balíčky NuGet pro klienta | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Npm Package klienta | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Klientskou sadou Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Maven balíček [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ aplikace serveru | Technologie ASP.NET (System.Web) nebo samoobslužné hostování OWIN | ASP.NET Core |
| Podporované serverové platformy | Rozhraní .NET framework 4.5 nebo novější | Rozhraní .NET framework 4.6.1 nebo novější<br>.NET core 2.1 nebo novější |

## <a name="feature-differences"></a>Rozdíly ve funkcích

### <a name="automatic-reconnects"></a>Automatické připojování

Automatické připojování nepodporují funkce SignalR technologie ASP.NET Core. Pokud klient je odpojen, uživatel musí explicitně spustit nové připojení, aby připojení bylo možné. V funkce SignalR technologie ASP.NET SignalR pokusí znovu připojit k serveru, pokud připojení se ukončí. 

### <a name="protocol-support"></a>Podpora protokolu

Funkce SignalR technologie ASP.NET Core podporuje JSON, jakož i nové binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol). Kromě toho je možné vytvářet vlastní protokoly.

### <a name="transports"></a>Přenosy

Přenos navždy rámce není podporován v knihovně SignalR technologie ASP.NET Core.

## <a name="differences-on-the-server"></a>Rozdíly na serveru

Jsou součástí knihoven na straně serveru funkce SignalR technologie ASP.NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) balíček, který je součástí **webové aplikace ASP.NET Core** šablony pro syntaxi Razor a MVC projekty.

Funkce SignalR technologie ASP.NET Core je middleware ASP.NET Core, takže musí být nakonfigurovaný pomocí volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

Konfigurace směrování, mapování tras k rozbočovačům uvnitř [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) volání metody `Startup.Configure` metoda.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a>Rychlé relace

Model horizontálním navýšením kapacity pro funkci SignalR technologie ASP.NET umožňuje klientům znovu připojit a odesílání zpráv na libovolný server ve farmě. V knihovně SignalR technologie ASP.NET Core musí klient pracovat na stejném serveru po dobu trvání připojení. Pro škálování, použití Redis, to znamená, že se vyžaduje rychlé relace. Pro práci s horizontálním navýšením kapacity [služby Azure SignalR](/azure/azure-signalr/), rychlé relace nejsou vyžadovány, protože služba zpracovává připojení ke klientům. 

### <a name="single-hub-per-connection"></a>Jedno Centrum za připojení

Funkce SignalR technologie ASP.NET Core je zjednodušený model připojení. Připojení jsou provedeny přímo do jednoho rozbočovače, nikoli jednoho připojení používá sdílet přístup k více rozbočovače.

### <a name="streaming"></a>Streamování

ASP.NET Core SignalR teď podporuje [streamovaná data](xref:signalr/streaming) z rozbočovače klientovi.

### <a name="state"></a>Stav

Umožňuje předat libovolný stav mezi klienty a centrum (často označované jako HubState) byla odebrána a také podporu pro zprávy o průběhu. V tuto chvíli není nevyskytují proxy rozbočovače.

### <a name="persistentconnection-removal"></a>Odebrání PersistentConnection

V knihovně SignalR technologie ASP.NET Core [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) třída odebrala. 

### <a name="globalhost"></a>GlobalHost

ASP.NET Core je integrovaný do rozhraní injektáž závislostí (DI). Služby využívat pro přístup k DI [HubContext](xref:signalr/hubcontext). `GlobalHost` Objekt, který se používá v knihovně SignalR technologie ASP.NET k získání `HubContext` neexistuje v knihovně SignalR technologie ASP.NET Core.

### <a name="hubpipeline"></a>Kanálu rozbočovače přidán

Funkce SignalR technologie ASP.NET Core nemá podporu `HubPipeline` moduly.

## <a name="differences-on-the-client"></a>Rozdíly v klientovi

### <a name="typescript"></a>TypeScript

Klient funkce SignalR technologie ASP.NET Core je napsána v [TypeScript](https://www.typescriptlang.org/). Můžete psát v jazyce JavaScript nebo TypeScript při použití [javascriptový klient](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScript klienta je hostovaná na [npm](https://www.npmjs.com/)

V předchozích verzích klienta JavaScript byl získán prostřednictvím balíčku NuGet v sadě Visual Studio. Verze jádra [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) balíčku npm obsahuje knihovny jazyka JavaScript. Není součástí tohoto balíčku **webové aplikace ASP.NET Core** šablony. Získejte a nainstalujte pomocí npm `@aspnet/signalr` balíčku npm.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

Závislost na jQuery byla odebrána, ale projekty můžete dál používat jQuery.

### <a name="internet-explorer-support"></a>Podpora aplikace Internet Explorer

Funkce SignalR technologie ASP.NET Core vyžaduje Microsoft Internet Explorer 11 nebo novější (funkce SignalR technologie ASP.NET podporovány Microsoft Internet Explorer 8 a novější).

### <a name="javascript-client-method-syntax"></a>Syntaxe využívající metody JavaScript klienta

Syntaxe jazyka JavaScript byl změněn z předchozí verze funkce SignalR. Místo použití `$connection` objektu, vytvořte pomocí připojení [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) rozhraní API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Použití [na](/javascript/api/@aspnet/signalr/HubConnection#on) metoda určují metody klienta můžete volání rozbočovače.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

Po vytvoření metody klienta, spusťte připojení rozbočovače. Řetězce [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodou protokolu nebo zpracování chyb.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>Proxy servery hub

Proxy servery hub už nebude automaticky generovány. Místo toho název metody je předán [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.

### <a name="net-and-other-clients"></a>.NET a další klienti

`Microsoft.AspNetCore.SignalR.Client` Balíček NuGet obsahuje klientské knihovny .NET pro funkci SignalR technologie ASP.NET Core.

Použití [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) vytvářet a sestavovat instanci, připojení k rozbočovači.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Rozdíly horizontálním navýšením kapacity

Funkce SignalR technologie ASP.NET podporuje SQL Server a Redis. Funkce SignalR technologie ASP.NET Core podporuje služby Azure SignalR a Redis.

### <a name="aspnet"></a>ASP.NET

* [Škálování aplikace SignalR službou Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Škálování aplikace SignalR službou Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [Škálování aplikace SignalR službou SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Služba Azure SignalR](/azure/azure-signalr/)
* [Redis propojovací rozhraní systému](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Podporované platformy](xref:signalr/supported-platforms)
