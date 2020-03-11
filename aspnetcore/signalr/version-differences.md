---
title: Rozdíly mezi SignalR a ASP.NET Core SignalR
author: bradygaster
description: Rozdíly mezi SignalR a ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: cca9a0cb0c46fc25eb5d1f7127d31fd3ab92f0b4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663546"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>Rozdíly mezi signálem ASP.NET a signálem ASP.NET Core

Signál ASP.NET Core není kompatibilní s klienty nebo servery pro signál ASP.NET. Tento článek obsahuje podrobnosti o funkcích, které byly v ASP.NET Coreovém signalizaci odebrány nebo změněny.

## <a name="how-to-identify-the-signalr-version"></a>Jak identifikovat verzi signalizace

::: moniker range=">= aspnetcore-3.0"

|                      | Funkce SignalR technologie ASP.NET | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft. AspNet. Signaler](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Žádné. Je součástí sdílené architektury [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| Klientské balíčky NuGet | [Microsoft. AspNet. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. Signaler. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. Signaler. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript – balíček npm klienta | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | Jádro ASP.NET |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Core 3,0 nebo novější |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | Funkce SignalR technologie ASP.NET | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Klientské balíčky NuGet | [Microsoft. AspNet.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript – balíček npm klienta | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | Jádro ASP.NET |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Framework 4.6.1 nebo novější<br>.NET Core 2,1 nebo novější |

::: moniker-end

## <a name="feature-differences"></a>Rozdíly ve funkcích

### <a name="automatic-reconnects"></a>Automatické opětovné připojení

::: moniker range=">= aspnetcore-3.0"

V ASP.NET SignalR:

* Ve výchozím nastavení se SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené. 

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

Před ASP.NET Core 3,0 nepodporuje SignalR automatické opětovné připojení. Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, aby se znovu připojil. V ASP.NET SignalRse SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené.

::: moniker-end

### <a name="protocol-support"></a>Podpora protokolů

ASP.NET Core SignalR podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol). Navíc můžete vytvářet vlastní protokoly.

### <a name="transports"></a>Přenosy

Přenos snímků navždy se v ASP.NET Core SignalRnepodporuje.

## <a name="differences-on-the-server"></a>Rozdíly na serveru

ASP.NET Core SignalR knihovny na straně serveru jsou součástí [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), který se používá v šabloně **webové aplikace ASP.NET Core** pro projekty Razor a MVC.

ASP.NET Core SignalR je ASP.NET Core middleware. Musí být nakonfigurováno voláním <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> v `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> v metodě `Startup.Configure`.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> v metodě `Startup.Configure`.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Rychlé relace

Model škálování pro ASP.NET SignalR umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě. V ASP.NET Core SignalRmusí klient spolupracovat se stejným serverem po dobu trvání připojení. Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace. Pro škálování pomocí [služby Azure SignalR](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.

### <a name="single-hub-per-connection"></a>Jedno centrum na připojení

V ASP.NET Core SignalRbyl model připojení zjednodušený. Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.

### <a name="streaming"></a>Streamování

ASP.NET Core SignalR teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.

### <a name="state"></a>Stav

Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označované jako `HubState`) a také podporovat zprávy o průběhu. V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.

### <a name="persistentconnection-removal"></a>Odebrání PersistentConnection

V ASP.NET Core SignalRbyla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .

### <a name="globalhost"></a>GlobalHost

ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní. Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di. Objekt `GlobalHost`, který se používá v ASP.NET SignalR k získání `HubContext` v ASP.NET Core SignalRneexistuje.

### <a name="hubpipeline"></a>HubPipeline

SignalR ASP.NET Core nemá podporu pro moduly `HubPipeline`.

## <a name="differences-on-the-client"></a>Rozdíly na klientovi

### <a name="typescript"></a>TypeScript

Klient SignalR ASP.NET Core je napsán v [TypeScript](https://www.typescriptlang.org/). Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScriptový klient je hostovaný na npm

::: moniker range=">= aspnetcore-3.0"

Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. V ASP.NET Core verzích obsahuje balíček [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . K získání a instalaci balíčku `@microsoft/signalr` npm použijte npm.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. V ASP.NET Core verzích obsahuje balíček [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . K získání a instalaci balíčku `@aspnet/signalr` npm použijte npm.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.

### <a name="internet-explorer-support"></a>Podpora aplikace Internet Explorer

ASP.NET Core SignalR vyžaduje Microsoft Internet Explorer 11 nebo novější (ASP.NET SignalR podporuje Microsoft Internet Explorer 8 a novější).

### <a name="javascript-client-method-syntax"></a>Syntaxe metody klienta jazyka JavaScript

::: moniker range=">= aspnetcore-3.0"

Syntaxe jazyka JavaScript se změnila z ASP.NET verze SignalR. Místo použití objektu `$connection` vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Použijte metodu [on](/javascript/api/@microsoft/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Syntaxe jazyka JavaScript se změnila z ASP.NET verze SignalR. Místo použití objektu `$connection` vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.

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

[Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Balíček NuGet klienta obsahuje klientské knihovny .NET pro SignalRASP.NET Core.

K vytvoření a vytvoření instance připojení k centru použijte <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Rozdíly ve škálování

ASP.NET SignalR podporuje SQL Server a Redis. ASP.NET Core SignalR podporuje službu Azure SignalR a Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalR škálování s využitím Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR škálování pomocí Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR škálování s využitím SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>Jádro ASP.NET

* [Služba Azure SignalR](/azure/azure-signalr/)
* [Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Javascriptový klient](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Podporované platformy](xref:signalr/supported-platforms)
