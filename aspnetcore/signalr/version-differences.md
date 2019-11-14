---
title: Rozdíly mezi SignalR a ASP.NET Core SignalR
author: bradygaster
description: Rozdíly mezi SignalR a ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963726"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>Rozdíly mezi ASP.NET SignalR a ASP.NET Core SignalR

ASP.NET Core SignalR není kompatibilní s klienty nebo servery pro ASP.NET SignalR. Tento článek obsahuje podrobnosti o funkcích, které se v ASP.NET Core SignalRodebraly nebo změnily.

## <a name="how-to-identify-the-opno-locsignalr-version"></a>Jak identifikovat SignalR verzi

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Balíček NuGet serveru | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Klientské balíčky NuGet | [Microsoft. AspNet.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Balíček npm klienta | [SignalR](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Klient Java | [Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)  | Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Typ serverové aplikace | ASP.NET (System. Web) nebo OWIN – samoobslužné hostování | ASP.NET Core |
| Podporované serverové platformy | .NET Framework 4,5 nebo novější | .NET Framework 4.6.1 nebo novější<br>.NET Core 2,1 nebo novější |

## <a name="feature-differences"></a>Rozdíly ve funkcích

### <a name="automatic-reconnects"></a>Automatické opětovné připojení

Automatické opětovné připojení nejsou v ASP.NET Core SignalRpodporovaná. Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, pokud se chce znovu připojit. V ASP.NET SignalRse SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené.

### <a name="protocol-support"></a>Podpora protokolů

ASP.NET Core SignalR podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol). Navíc můžete vytvářet vlastní protokoly.

### <a name="transports"></a>Přenosy

Přenos snímků navždy se v ASP.NET Core SignalRnepodporuje.

## <a name="differences-on-the-server"></a>Rozdíly na serveru

ASP.NET Core SignalR knihovny na straně serveru jsou součástí balíčku [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , který je součástí šablony **webové aplikace ASP.NET Core** pro projekty Razor a MVC.

ASP.NET Core SignalR je ASP.NET Core middleware, takže se musí nakonfigurovat voláním [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) v metodě `Startup.Configure`.


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) v metodě `Startup.Configure`.

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

Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným jako HubState) a také podporovat zprávy o průběhu. V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.

### <a name="persistentconnection-removal"></a>Odebrání PersistentConnection

V ASP.NET Core SignalRbyla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .

### <a name="globalhost"></a>GlobalHost

ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní. Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di. Objekt `GlobalHost`, který se používá v ASP.NET SignalR k získání `HubContext` v ASP.NET Core SignalRneexistuje.

### <a name="hubpipeline"></a>HubPipeline

SignalR ASP.NET Core nemá podporu pro moduly `HubPipeline`.

## <a name="differences-on-the-client"></a>Rozdíly na klientovi

### <a name="typescript"></a>TypeScript

Klient SignalR ASP.NET Core je napsán v [TypeScript](https://www.typescriptlang.org/). Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScriptový klient je hostovaný na [npm](https://www.npmjs.com/)

V předchozích verzích byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio. Pro základní verze obsahuje balíček [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm knihovny JavaScriptu. Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** . K získání a instalaci balíčku `@aspnet/signalr` npm použijte npm.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.

### <a name="internet-explorer-support"></a>Podpora aplikace Internet Explorer

ASP.NET Core SignalR vyžaduje Microsoft Internet Explorer 11 nebo novější (ASP.NET SignalR podporuje Microsoft Internet Explorer 8 a novější).

### <a name="javascript-client-method-syntax"></a>Syntaxe metody klienta jazyka JavaScript

Syntaxe jazyka JavaScript se změnila z předchozí verze SignalR. Místo použití objektu `$connection` vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.

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

Balíček NuGet `Microsoft.AspNetCore.SignalR.Client` obsahuje klientské knihovny .NET pro SignalRASP.NET Core.

Pomocí [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) můžete vytvořit a sestavit instanci připojení k rozbočovači.

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

### <a name="aspnet-core"></a>ASP.NET Core

* [Služba Azure SignalR](/azure/azure-signalr/)
* [Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Podporované platformy](xref:signalr/supported-platforms)
