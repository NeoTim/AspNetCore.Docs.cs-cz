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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="c5f90-103">Konfigurace signalizace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5f90-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c5f90-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="c5f90-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c5f90-105">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="c5f90-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c5f90-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="c5f90-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5f90-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c5f90-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c5f90-108">`AddJsonProtocol` lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c5f90-109">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c5f90-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je objekt <xref:System.Text.Json.JsonSerializerOptions> `System.Text.Json`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="c5f90-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c5f90-111">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="c5f90-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c5f90-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c5f90-113">V klientovi .NET existuje stejná rozšiřující metoda `AddJsonProtocol` v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c5f90-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c5f90-114">Pro vyřešení metody rozšíření je nutné importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c5f90-115">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="c5f90-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c5f90-116">Pokud potřebujete funkce `Newtonsoft.Json`, které nejsou podporované v `System.Text.Json`, přečtěte si téma [Přepnutí na Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="c5f90-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c5f90-117">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c5f90-118">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c5f90-119">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="c5f90-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c5f90-120">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c5f90-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c5f90-121">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" místo výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c5f90-122">V klientovi .NET existuje stejná rozšiřující metoda `AddJsonProtocol` v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="c5f90-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c5f90-123">Pro vyřešení metody rozšíření je nutné importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c5f90-124">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5f90-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c5f90-125">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="c5f90-125">MessagePack serialization options</span></span>

<span data-ttu-id="c5f90-126">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c5f90-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c5f90-127">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="c5f90-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c5f90-128">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5f90-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c5f90-129">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="c5f90-129">Configure server options</span></span>

<span data-ttu-id="c5f90-130">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="c5f90-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="c5f90-131">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-131">Option</span></span> | <span data-ttu-id="c5f90-132">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-132">Default Value</span></span> | <span data-ttu-id="c5f90-133">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c5f90-134">30 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-134">30 seconds</span></span> | <span data-ttu-id="c5f90-135">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="c5f90-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c5f90-136">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="c5f90-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c5f90-137">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c5f90-138">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-138">15 seconds</span></span> | <span data-ttu-id="c5f90-139">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="c5f90-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c5f90-140">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-141">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c5f90-142">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-142">15 seconds</span></span> | <span data-ttu-id="c5f90-143">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="c5f90-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c5f90-144">Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c5f90-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c5f90-145">Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c5f90-146">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="c5f90-146">All installed protocols</span></span> | <span data-ttu-id="c5f90-147">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="c5f90-147">Protocols supported by this hub.</span></span> <span data-ttu-id="c5f90-148">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="c5f90-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c5f90-149">Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c5f90-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c5f90-150">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="c5f90-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c5f90-151">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="c5f90-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c5f90-152">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c5f90-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="c5f90-153">32 KB</span></span> | <span data-ttu-id="c5f90-154">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="c5f90-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="c5f90-155">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-155">Option</span></span> | <span data-ttu-id="c5f90-156">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-156">Default Value</span></span> | <span data-ttu-id="c5f90-157">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c5f90-158">30 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-158">30 seconds</span></span> | <span data-ttu-id="c5f90-159">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="c5f90-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c5f90-160">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="c5f90-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c5f90-161">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c5f90-162">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-162">15 seconds</span></span> | <span data-ttu-id="c5f90-163">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="c5f90-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c5f90-164">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-165">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c5f90-166">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-166">15 seconds</span></span> | <span data-ttu-id="c5f90-167">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="c5f90-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c5f90-168">Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c5f90-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c5f90-169">Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c5f90-170">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="c5f90-170">All installed protocols</span></span> | <span data-ttu-id="c5f90-171">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="c5f90-171">Protocols supported by this hub.</span></span> <span data-ttu-id="c5f90-172">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="c5f90-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c5f90-173">Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c5f90-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c5f90-174">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="c5f90-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="c5f90-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-175">Option</span></span> | <span data-ttu-id="c5f90-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-176">Default Value</span></span> | <span data-ttu-id="c5f90-177">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c5f90-178">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-178">15 seconds</span></span> | <span data-ttu-id="c5f90-179">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="c5f90-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c5f90-180">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-181">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c5f90-182">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-182">15 seconds</span></span> | <span data-ttu-id="c5f90-183">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="c5f90-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c5f90-184">Při změně `KeepAliveInterval` změňte nastavení `ServerTimeout` @ no__t-2 @ no__t-3 na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c5f90-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c5f90-185">Doporučená hodnota `ServerTimeout` @ no__t-1 @ no__t-2 zdvojnásobí hodnotu `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c5f90-186">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="c5f90-186">All installed protocols</span></span> | <span data-ttu-id="c5f90-187">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="c5f90-187">Protocols supported by this hub.</span></span> <span data-ttu-id="c5f90-188">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="c5f90-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c5f90-189">Pokud je `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c5f90-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c5f90-190">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="c5f90-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="c5f90-191">Možnosti lze nakonfigurovat pro všechna centra tím, že zadáte delegáty možností pro volání `AddSignalR` v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c5f90-192">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="c5f90-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c5f90-193">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="c5f90-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5f90-194">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c5f90-195">Tyto možnosti jsou nakonfigurovány předáním delegáta do [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c5f90-196">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c5f90-197">Tyto možnosti jsou nakonfigurovány předáním delegáta do [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c5f90-198">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="c5f90-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="c5f90-199">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-199">Option</span></span> | <span data-ttu-id="c5f90-200">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-200">Default Value</span></span> | <span data-ttu-id="c5f90-201">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c5f90-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="c5f90-202">32 KB</span></span> | <span data-ttu-id="c5f90-203">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="c5f90-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c5f90-204">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c5f90-205">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="c5f90-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c5f90-206">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="c5f90-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c5f90-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="c5f90-207">32 KB</span></span> | <span data-ttu-id="c5f90-208">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="c5f90-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c5f90-209">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c5f90-210">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="c5f90-210">All Transports are enabled.</span></span> | <span data-ttu-id="c5f90-211">Bitové příznaky výčtu hodnot `HttpTransportType`, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="c5f90-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c5f90-212">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="c5f90-212">See below.</span></span> | <span data-ttu-id="c5f90-213">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="c5f90-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c5f90-214">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="c5f90-214">See below.</span></span> | <span data-ttu-id="c5f90-215">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="c5f90-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="c5f90-216">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-216">Option</span></span> | <span data-ttu-id="c5f90-217">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-217">Default Value</span></span> | <span data-ttu-id="c5f90-218">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c5f90-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="c5f90-219">32 KB</span></span> | <span data-ttu-id="c5f90-220">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c5f90-221">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c5f90-222">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="c5f90-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c5f90-223">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="c5f90-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c5f90-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="c5f90-224">32 KB</span></span> | <span data-ttu-id="c5f90-225">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c5f90-226">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="c5f90-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c5f90-227">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="c5f90-227">All Transports are enabled.</span></span> | <span data-ttu-id="c5f90-228">Bitové příznaky výčtu hodnot `HttpTransportType`, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="c5f90-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c5f90-229">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="c5f90-229">See below.</span></span> | <span data-ttu-id="c5f90-230">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="c5f90-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c5f90-231">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="c5f90-231">See below.</span></span> | <span data-ttu-id="c5f90-232">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="c5f90-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="c5f90-233">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c5f90-234">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-234">Option</span></span> | <span data-ttu-id="c5f90-235">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-235">Default Value</span></span> | <span data-ttu-id="c5f90-236">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c5f90-237">90 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-237">90 seconds</span></span> | <span data-ttu-id="c5f90-238">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="c5f90-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c5f90-239">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="c5f90-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c5f90-240">Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c5f90-241">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-241">Option</span></span> | <span data-ttu-id="c5f90-242">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-242">Default Value</span></span> | <span data-ttu-id="c5f90-243">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c5f90-244">5 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-244">5 seconds</span></span> | <span data-ttu-id="c5f90-245">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="c5f90-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c5f90-246">Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c5f90-247">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="c5f90-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c5f90-248">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="c5f90-248">Configure client options</span></span>

<span data-ttu-id="c5f90-249">Možnosti klienta lze nakonfigurovat u typu @no__t 0 (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c5f90-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c5f90-250">Je také k dispozici v klientovi Java, ale podtřídou `HttpHubConnectionBuilder` je to, co obsahuje možnosti konfigurace tvůrce a také na samotné `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c5f90-251">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="c5f90-251">Configure logging</span></span>

<span data-ttu-id="c5f90-252">Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c5f90-253">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c5f90-254">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="c5f90-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c5f90-255">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="c5f90-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c5f90-256">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="c5f90-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c5f90-257">Chcete-li například povolit protokolování konzoly, nainstalujte balíček NuGet `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c5f90-258">Zavolejte metodu rozšíření `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c5f90-259">V klientovi jazyka JavaScript existuje podobná metoda `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c5f90-260">Zadejte hodnotu `LogLevel` určující minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="c5f90-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c5f90-261">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c5f90-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5f90-262">Místo hodnoty @no__t 0 můžete také zadat hodnotu `string` reprezentující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c5f90-263">To je užitečné při konfiguraci protokolování signalizace v prostředích, kde nemáte přístup k konstantám `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c5f90-264">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-264">The following table lists the available log levels.</span></span> <span data-ttu-id="c5f90-265">Hodnota, kterou zadáte pro `configureLogging` nastaví **minimální** úroveň protokolu, která bude protokolována.</span><span class="sxs-lookup"><span data-stu-id="c5f90-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c5f90-266">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="c5f90-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c5f90-267">Řetězec</span><span class="sxs-lookup"><span data-stu-id="c5f90-267">String</span></span>                      | <span data-ttu-id="c5f90-268">logLevel</span><span class="sxs-lookup"><span data-stu-id="c5f90-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c5f90-269">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="c5f90-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c5f90-270">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="c5f90-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="c5f90-271">Pokud chcete protokolování zcela zakázat, zadejte v metodě `configureLogging` `signalR.LogLevel.None`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c5f90-272">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="c5f90-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c5f90-273">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="c5f90-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c5f90-274">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="c5f90-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c5f90-275">Následující fragment kódu ukazuje, jak použít `java.util.logging` u klienta signalizace v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="c5f90-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c5f90-276">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="c5f90-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c5f90-277">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="c5f90-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c5f90-278">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="c5f90-278">Configure allowed transports</span></span>

<span data-ttu-id="c5f90-279">Transporty používané signálem lze nakonfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="c5f90-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c5f90-280">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="c5f90-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c5f90-281">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="c5f90-281">All transports are enabled by default.</span></span>

<span data-ttu-id="c5f90-282">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="c5f90-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c5f90-283">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` u objektu Options, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c5f90-284">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="c5f90-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="c5f90-285">V klientovi Java se přenos vybere s metodou `withTransport` na `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c5f90-286">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c5f90-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c5f90-287">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="c5f90-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c5f90-288">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="c5f90-288">Configure bearer authentication</span></span>

<span data-ttu-id="c5f90-289">K poskytnutí ověřovacích dat spolu s požadavky na signalizaci použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="c5f90-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c5f90-290">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím hlavičky `Authorization` s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="c5f90-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c5f90-291">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="c5f90-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c5f90-292">V těchto případech je přístupový token poskytnutý jako hodnota řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c5f90-293">V klientovi .NET je možné zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c5f90-294">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c5f90-295">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c5f90-296">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) použijte k poskytnutí [RxJava](https://github.com/ReactiveX/RxJava) [jednoho no__t-3String >](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="c5f90-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c5f90-297">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="c5f90-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c5f90-298">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="c5f90-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c5f90-299">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na samotném objektu @no__t 0:</span><span class="sxs-lookup"><span data-stu-id="c5f90-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="c5f90-300">.NET</span><span class="sxs-lookup"><span data-stu-id="c5f90-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c5f90-301">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-301">Option</span></span> | <span data-ttu-id="c5f90-302">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-302">Default value</span></span> | <span data-ttu-id="c5f90-303">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c5f90-304">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-305">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-305">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-306">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="c5f90-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c5f90-307">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-308">Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c5f90-309">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-309">15 seconds</span></span> | <span data-ttu-id="c5f90-310">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="c5f90-311">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="c5f90-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c5f90-312">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-313">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c5f90-314">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-314">15 seconds</span></span> | <span data-ttu-id="c5f90-315">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="c5f90-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c5f90-316">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c5f90-317">Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="c5f90-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c5f90-318">V klientovi .NET jsou hodnoty časového limitu zadány jako hodnoty `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="c5f90-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5f90-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c5f90-320">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-320">Option</span></span> | <span data-ttu-id="c5f90-321">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-321">Default value</span></span> | <span data-ttu-id="c5f90-322">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c5f90-323">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-324">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-324">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-325">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c5f90-326">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-327">Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c5f90-328">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="c5f90-329">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="c5f90-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c5f90-330">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c5f90-331">Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="c5f90-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="c5f90-332">Java</span><span class="sxs-lookup"><span data-stu-id="c5f90-332">Java</span></span>](#tab/java)

| <span data-ttu-id="c5f90-333">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-333">Option</span></span> | <span data-ttu-id="c5f90-334">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-334">Default value</span></span> | <span data-ttu-id="c5f90-335">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c5f90-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c5f90-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c5f90-337">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-338">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-338">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-339">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c5f90-340">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-341">Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c5f90-342">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-342">15 seconds</span></span> | <span data-ttu-id="c5f90-343">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="c5f90-344">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c5f90-345">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-346">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c5f90-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c5f90-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c5f90-348">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-349">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="c5f90-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c5f90-350">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c5f90-351">Pokud klient neodeslal zprávu do `ClientTimeoutInterval` nastaveného na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="c5f90-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="c5f90-352">.NET</span><span class="sxs-lookup"><span data-stu-id="c5f90-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c5f90-353">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-353">Option</span></span> | <span data-ttu-id="c5f90-354">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-354">Default value</span></span> | <span data-ttu-id="c5f90-355">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c5f90-356">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-357">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-357">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-358">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="c5f90-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c5f90-359">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-360">Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c5f90-361">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-361">15 seconds</span></span> | <span data-ttu-id="c5f90-362">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="c5f90-363">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="c5f90-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c5f90-364">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-365">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c5f90-366">V klientovi .NET jsou hodnoty časového limitu zadány jako hodnoty `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="c5f90-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5f90-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c5f90-368">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-368">Option</span></span> | <span data-ttu-id="c5f90-369">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-369">Default value</span></span> | <span data-ttu-id="c5f90-370">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c5f90-371">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-372">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-372">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-373">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c5f90-374">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-375">Doporučená hodnota je číslo alespoň dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="c5f90-376">Java</span><span class="sxs-lookup"><span data-stu-id="c5f90-376">Java</span></span>](#tab/java)

| <span data-ttu-id="c5f90-377">Možnost</span><span class="sxs-lookup"><span data-stu-id="c5f90-377">Option</span></span> | <span data-ttu-id="c5f90-378">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-378">Default value</span></span> | <span data-ttu-id="c5f90-379">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c5f90-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c5f90-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c5f90-381">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="c5f90-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c5f90-382">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-382">Timeout for server activity.</span></span> <span data-ttu-id="c5f90-383">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c5f90-384">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="c5f90-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c5f90-385">Doporučená hodnota je číslo minimálně dvojnásobku hodnoty `KeepAliveInterval` serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="c5f90-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c5f90-386">15 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-386">15 seconds</span></span> | <span data-ttu-id="c5f90-387">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="c5f90-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="c5f90-388">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c5f90-389">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="c5f90-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c5f90-390">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="c5f90-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="c5f90-391">Konfigurovat další možnosti</span><span class="sxs-lookup"><span data-stu-id="c5f90-391">Configure additional options</span></span>

<span data-ttu-id="c5f90-392">Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` v JavaScriptu) na `HubConnectionBuilder` nebo v různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="c5f90-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="c5f90-393">.NET</span><span class="sxs-lookup"><span data-stu-id="c5f90-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c5f90-394">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="c5f90-394">.NET Option</span></span> |  <span data-ttu-id="c5f90-395">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-395">Default value</span></span> | <span data-ttu-id="c5f90-396">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c5f90-397">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5f90-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c5f90-398">Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="c5f90-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c5f90-399">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="c5f90-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c5f90-400">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="c5f90-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c5f90-401">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="c5f90-401">Empty</span></span> | <span data-ttu-id="c5f90-402">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="c5f90-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c5f90-403">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="c5f90-403">Empty</span></span> | <span data-ttu-id="c5f90-404">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="c5f90-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c5f90-405">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="c5f90-405">Empty</span></span> | <span data-ttu-id="c5f90-406">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="c5f90-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c5f90-407">5 sekund</span><span class="sxs-lookup"><span data-stu-id="c5f90-407">5 seconds</span></span> | <span data-ttu-id="c5f90-408">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c5f90-408">WebSockets only.</span></span> <span data-ttu-id="c5f90-409">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="c5f90-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c5f90-410">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="c5f90-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c5f90-411">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="c5f90-411">Empty</span></span> | <span data-ttu-id="c5f90-412">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="c5f90-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c5f90-413">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5f90-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c5f90-414">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c5f90-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="c5f90-415">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="c5f90-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c5f90-416">Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c5f90-417">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="c5f90-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c5f90-418">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5f90-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c5f90-419">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c5f90-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c5f90-420">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="c5f90-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c5f90-421">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c5f90-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c5f90-422">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="c5f90-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="c5f90-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5f90-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c5f90-424">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c5f90-424">JavaScript Option</span></span> | <span data-ttu-id="c5f90-425">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-425">Default Value</span></span> | <span data-ttu-id="c5f90-426">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c5f90-427">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5f90-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c5f90-428">Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="c5f90-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c5f90-429">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="c5f90-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c5f90-430">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="c5f90-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="c5f90-431">Java</span><span class="sxs-lookup"><span data-stu-id="c5f90-431">Java</span></span>](#tab/java)

| <span data-ttu-id="c5f90-432">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="c5f90-432">Java Option</span></span> | <span data-ttu-id="c5f90-433">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="c5f90-433">Default Value</span></span> | <span data-ttu-id="c5f90-434">Popis</span><span class="sxs-lookup"><span data-stu-id="c5f90-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c5f90-435">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="c5f90-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c5f90-436">Nastavte tuto hodnotu na `true`, chcete-li přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="c5f90-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c5f90-437">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="c5f90-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c5f90-438">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="c5f90-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c5f90-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c5f90-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c5f90-440">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="c5f90-440">Empty</span></span> | <span data-ttu-id="c5f90-441">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="c5f90-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c5f90-442">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c5f90-443">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="c5f90-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c5f90-444">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`.</span><span class="sxs-lookup"><span data-stu-id="c5f90-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c5f90-445">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="c5f90-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
