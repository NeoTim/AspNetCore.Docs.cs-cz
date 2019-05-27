---
title: gRPC konfigurace ASP.NET Core
author: jamesnk
description: Zjistěte, jak nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041885"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="903b3-103">gRPC konfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="903b3-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="903b3-104">Nakonfigurujte možnosti služby</span><span class="sxs-lookup"><span data-stu-id="903b3-104">Configure services options</span></span>

<span data-ttu-id="903b3-105">Následující tabulka popisuje možnosti pro konfiguraci služby gRPC:</span><span class="sxs-lookup"><span data-stu-id="903b3-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="903b3-106">Možnost</span><span class="sxs-lookup"><span data-stu-id="903b3-106">Option</span></span> | <span data-ttu-id="903b3-107">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="903b3-107">Default Value</span></span> | <span data-ttu-id="903b3-108">Popis</span><span class="sxs-lookup"><span data-stu-id="903b3-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="903b3-109">Maximální velikost zprávy v bajtech odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="903b3-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="903b3-110">Došlo k pokusu o odeslání zprávy, která překračuje velikost výsledky nakonfigurovanou maximální zprávy výjimek.</span><span class="sxs-lookup"><span data-stu-id="903b3-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="903b3-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="903b3-111">4 MB</span></span> | <span data-ttu-id="903b3-112">Maximální velikost zprávy v bajtech, které lze přijímat pomocí serveru.</span><span class="sxs-lookup"><span data-stu-id="903b3-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="903b3-113">Pokud server přijme zprávu, která překračuje tento limit, dojde k výjimce.</span><span class="sxs-lookup"><span data-stu-id="903b3-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="903b3-114">Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti.</span><span class="sxs-lookup"><span data-stu-id="903b3-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="903b3-115">Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě služby.</span><span class="sxs-lookup"><span data-stu-id="903b3-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="903b3-116">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="903b3-116">The default is `false`.</span></span> <span data-ttu-id="903b3-117">Nastavení na `true` může způsobit únik citlivých informací.</span><span class="sxs-lookup"><span data-stu-id="903b3-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="903b3-118">gzip</span><span class="sxs-lookup"><span data-stu-id="903b3-118">gzip</span></span> | <span data-ttu-id="903b3-119">Kolekce zprostředkovatelů komprese používá při komprimaci a dekomprimaci zprávy.</span><span class="sxs-lookup"><span data-stu-id="903b3-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="903b3-120">Poskytovatelé vlastního komprese můžete vytvořen a přidán do kolekce.</span><span class="sxs-lookup"><span data-stu-id="903b3-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="903b3-121">Výchozí nastavení nakonfigurován zprostředkovatel podporuje **gzip** komprese.</span><span class="sxs-lookup"><span data-stu-id="903b3-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="903b3-122">Komprese algoritmus používaný ke kompresi zpráv odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="903b3-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="903b3-123">Algoritmus musí odpovídat komprese zprostředkovatele v `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="903b3-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="903b3-124">Pro algorthm pro kompresi odpovědí musíte uvést podporuje algoritmus a odeslat ho klienta **grpc přijmout – kódování** záhlaví.</span><span class="sxs-lookup"><span data-stu-id="903b3-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="903b3-125">Úroveň komprese použitý ke kompresi zpráv odeslaných ze serveru.</span><span class="sxs-lookup"><span data-stu-id="903b3-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="903b3-126">Je možné nakonfigurovat možnosti pro všechny služby poskytnutím delegáta možnosti k `AddGrpc` volání v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="903b3-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="903b3-127">Možnosti pro jednu službu přepsat globální možnosti uvedené v `AddGrpc` a dá se nakonfigurovat pomocí `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="903b3-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a><span data-ttu-id="903b3-128">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="903b3-128">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
