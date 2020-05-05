---
title: Rozdíly mezi SignalR a ASP.NET CoreSignalR
author: bradygaster
description: Rozdíly mezi SignalR a ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772576"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Rozdíly mezi signálem ASP.NET a signálem ASP.NET Core

Signál ASP.NET Core není kompatibilní s klienty nebo servery pro signál ASP.NET. Tento článek obsahuje podrobnosti o funkcích, které byly v ASP.NET Coreovém signalizaci odebrány nebo změněny.

## <a name="how-to-identify-the-signalr-version"></a>Jak identifikovat verzi signalizace

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft. AspNet. Signaler](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Žádné. Je součástí sdílené architektury [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Klientské balíčky NuGet | [Microsoft. AspNet. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. Signaler. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript – balíček npm klienta | [SignalR](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | Jádro ASP.NET |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Core 3,0 nebo novější |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Klientské balíčky NuGet | [Microsoft. AspNet. SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript – balíček npm klienta | [SignalR](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | Jádro ASP.NET |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Framework 4.6.1 nebo novější<br>.NET Core 2,1 nebo novější |

::: moniker-end

## <a name="feature-differences"></a>Rozdíly ve funkcích

### <a name="automatic-reconnects"></a>Automatické opětovné připojení

::: moniker range=">= aspnetcore-3.0"

V ASP.NET SignalR:

* Ve výchozím nastavení SignalR se aplikace pokusí znovu připojit k serveru, pokud je připojení zrušeno. 

V ASP.NET Core SignalR:

* Automatické opětovné připojení jsou výslovným souhlasem s [klientem rozhraní .NET](xref:signalr/dotnet-client#automatically-reconnect) i s [klientem JavaScriptu](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Před ASP.NET Core 3,0 SignalR nepodporuje automatické opětovné připojení. Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, aby se znovu připojil. V ASP.NET SignalRse SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené.

::: moniker-end

### <a name="protocol-support"></a>Podpora protokolu

ASP.NET Core SignalR podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol). Navíc můžete vytvářet vlastní protokoly.

### <a name="transports"></a>Přenosy

Přenos snímků navždy není v ASP.NET Core SignalRpodporován.

## <a name="differences-on-the-server"></a>Rozdíly na serveru

ASP.NET Core SignalR knihovny na straně serveru jsou součástí [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), který se používá v šabloně **webové aplikace ASP.NET Core** i pro Razor projekty MVC.

ASP.NET Core SignalR je ASP.NET Core middleware. Musí být nakonfigurováno voláním <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> v `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat směrování, namapujte trasy na <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> rozbočovače uvnitř volání `Startup.Configure` metody v metodě.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Chcete-li konfigurovat směrování, namapujte trasy na <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> rozbočovače uvnitř volání `Startup.Configure` metody v metodě.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Rychlé relace

Model horizontálního navýšení SignalR kapacity pro ASP.NET umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě. V ASP.NET Core SignalRmusí klient spolupracovat se stejným serverem po dobu trvání připojení. Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace. Pro škálování pomocí [služby Azure SignalR ](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.

### <a name="single-hub-per-connection"></a>Jedno centrum na připojení

V ASP.NET Core SignalRbyl model připojení zjednodušený. Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.

### <a name="streaming"></a>Streamování

ASP.NET Core SignalR teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.

### <a name="state"></a>Stav

Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným `HubState`jako) a také podporovat zprávy o průběhu. V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.

### <a name="persistentconnection-removal"></a>Odebrání PersistentConnection

V ASP.NET Core SignalRbyla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .

### <a name="globalhost"></a>GlobalHost

ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní. Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di. `GlobalHost` Objekt, který se používá v ASP.NET SignalR k získání `HubContext` , neexistuje v ASP.NET Core SignalR.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR nemá podporu pro `HubPipeline` moduly.

## <a name="differences-on-the-client"></a>Rozdíly na klientovi

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR klient je napsán v [TypeScript](https://www.typescriptlang.org/). Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScriptový klient je hostovaný na npm

::: moniker range=">= aspnetcore-3.0"

Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. V ASP.NET Core verzích obsahuje balíček [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . Použijte npm k získání a instalaci balíčku `@microsoft/signalr` npm.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. V ASP.NET Core verzích obsahuje balíček [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . Použijte npm k získání a instalaci balíčku `@aspnet/signalr` npm.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.

### <a name="internet-explorer-support"></a>Podpora aplikace Internet Explorer

ASP.NET Core SignalR vyžaduje aplikaci Microsoft Internet Explorer 11 nebo novější ( SignalR ASP.NET podporuje Microsoft Internet Explorer 8 a novější).

### <a name="javascript-client-method-syntax"></a>Syntaxe metody klienta jazyka JavaScript

::: moniker range=">= aspnetcore-3.0"

Syntaxe jazyka JavaScript se změnila z verze ASP.NET systému SignalR. Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Použijte metodu [on](/javascript/api/@microsoft/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Syntaxe jazyka JavaScript se změnila z verze ASP.NET systému SignalR. Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Použijte metodu [on](/javascript/api/@aspnet/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Po vytvoření metody klienta spusťte připojení k rozbočovači. Řetězení metody [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pro protokolování a zpracování chyb.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Proxy servery centra

::: moniker range=">= aspnetcore-3.0"

Proxy servery centra se už negenerují automaticky. Místo toho je název metody předán do [vyvolání](/javascript/api/@microsoft/signalr/hubconnection#invoke) rozhraní API jako řetězec.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Proxy servery centra se už negenerují automaticky. Místo toho je název metody předán do [vyvolání](/javascript/api/@aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET a další klienti

[Microsoft. AspNetCoreSignalR.. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Balíček NuGet klienta obsahuje klientské knihovny .NET pro ASP.NET Core SignalR.

Použijte <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> k vytvoření a sestavení instance připojení k rozbočovači.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Rozdíly ve škálování

ASP.NET SignalR podporuje SQL Server a Redis. ASP.NET Core SignalR podporuje Azure SignalR Service a Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalRhorizontální navýšení kapacity pomocí Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRhorizontální navýšení kapacity pomocí Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRhorizontální navýšení kapacity pomocí SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>Jádro ASP.NET

* [Služba SignalR Azure](/azure/azure-signalr/)
* [Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Podporované platformy](xref:signalr/supported-platforms)
