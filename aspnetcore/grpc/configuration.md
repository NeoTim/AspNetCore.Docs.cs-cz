---
title: gRPC konfigurace ASP.NET Core
author: jamesnk
description: Zjistěte, jak nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087350"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="52085-103">gRPC konfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52085-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="52085-104">Nakonfigurujte možnosti služby</span><span class="sxs-lookup"><span data-stu-id="52085-104">Configure services options</span></span>

<span data-ttu-id="52085-105">Následující tabulka popisuje možnosti pro konfiguraci služby gRPC:</span><span class="sxs-lookup"><span data-stu-id="52085-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="52085-106">Možnost</span><span class="sxs-lookup"><span data-stu-id="52085-106">Option</span></span> | <span data-ttu-id="52085-107">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="52085-107">Default Value</span></span> | <span data-ttu-id="52085-108">Popis</span><span class="sxs-lookup"><span data-stu-id="52085-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="52085-109">Maximální velikost zprávy v bajtech odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="52085-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="52085-110">Došlo k pokusu o odeslání zprávy, která překračuje velikost výsledky nakonfigurovanou maximální zprávy výjimek.</span><span class="sxs-lookup"><span data-stu-id="52085-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="52085-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="52085-111">4 MB</span></span> | <span data-ttu-id="52085-112">Maximální velikost zprávy v bajtech, které lze přijímat pomocí serveru.</span><span class="sxs-lookup"><span data-stu-id="52085-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="52085-113">Pokud server přijme zprávu, která překračuje tento limit, dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="52085-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="52085-114">Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti.</span><span class="sxs-lookup"><span data-stu-id="52085-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="52085-115">Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě služby.</span><span class="sxs-lookup"><span data-stu-id="52085-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="52085-116">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="52085-116">The default is `false`.</span></span> <span data-ttu-id="52085-117">Nastavení na `true` může způsobit únik citlivých informací.</span><span class="sxs-lookup"><span data-stu-id="52085-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="52085-118">gzip</span><span class="sxs-lookup"><span data-stu-id="52085-118">gzip</span></span> | <span data-ttu-id="52085-119">Kolekce zprostředkovatelů komprese používá při komprimaci a dekomprimaci zprávy.</span><span class="sxs-lookup"><span data-stu-id="52085-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="52085-120">Poskytovatelé vlastního komprese můžete vytvořen a přidán do kolekce.</span><span class="sxs-lookup"><span data-stu-id="52085-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="52085-121">Výchozí nastavení nakonfigurován zprostředkovatel podporuje **gzip** komprese.</span><span class="sxs-lookup"><span data-stu-id="52085-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="52085-122">Komprese algoritmus používaný ke kompresi zpráv odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="52085-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="52085-123">Algoritmus musí odpovídat komprese zprostředkovatele v `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="52085-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="52085-124">Pro algorthm pro kompresi odpovědí musíte uvést podporuje algoritmus a odeslat ho klienta **grpc přijmout – kódování** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="52085-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="52085-125">Úroveň komprese použitý ke kompresi zpráv odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="52085-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="52085-126">Je možné nakonfigurovat možnosti pro všechny služby poskytnutím delegáta možnosti k `AddGrpc` volání v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="52085-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="52085-127">Možnosti pro jednu službu přepsat globální možnosti uvedené v `AddGrpc` a dá se nakonfigurovat pomocí `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="52085-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a><span data-ttu-id="52085-128">Nakonfigurujte Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="52085-128">Configure Kestrel options</span></span>

<span data-ttu-id="52085-129">Kestrel serveru obsahuje možnosti konfigurace, které ovlivňují chování gRPC pro technologii ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="52085-129">Kestrel server has configuration options that affect the behavior of gRPC for ASP.NET.</span></span>

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="52085-130">Omezení četnosti data těla požadavku</span><span class="sxs-lookup"><span data-stu-id="52085-130">Request body data rate limit</span></span>

<span data-ttu-id="52085-131">Ve výchozím nastavení, Kestrel server ukládá [minimální požadavek tělo přenosová rychlost](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span><span class="sxs-lookup"><span data-stu-id="52085-131">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="52085-132">Pro klienty streamování a streaming volání duplexní režim nemusí být splněny tato sazba a připojení může být vypršení časového limitu. Minimální požadavku limitu přenosové rychlosti dat musí být zakázáno, pokud obsahuje služba gRPC klienta streamování a streaming volání duplexní režim:</span><span class="sxs-lookup"><span data-stu-id="52085-132">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="52085-133">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="52085-133">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
