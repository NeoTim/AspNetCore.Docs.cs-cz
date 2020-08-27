---
title: Komunikace mezi procesy pomocí gRPC
author: jamesnk
description: Naučte se používat gRPC pro komunikaci mezi procesy.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945702"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="5d5c6-103">Komunikace mezi procesy pomocí gRPC</span><span class="sxs-lookup"><span data-stu-id="5d5c6-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="5d5c6-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5d5c6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5d5c6-105">gRPC volání mezi klientem a službou se obvykle odesílají přes sokety TCP.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="5d5c6-106">Protokol TCP je ideální pro komunikaci přes síť, ale [komunikace mezi procesy (IPC)](https://wikipedia.org/wiki/Inter-process_communication) je efektivnější, pokud je klient a služba ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="5d5c6-107">Tento dokument vysvětluje, jak používat gRPC s vlastními přenosy ve scénářích IPC.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="5d5c6-108">Konfigurace serveru</span><span class="sxs-lookup"><span data-stu-id="5d5c6-108">Server configuration</span></span>

<span data-ttu-id="5d5c6-109">Vlastní přenos podporuje Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="5d5c6-110">Kestrel se konfiguruje v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5d5c6-110">Kestrel is configured in *Program.cs*:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="5d5c6-111">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="5d5c6-111">The preceding example:</span></span>

* <span data-ttu-id="5d5c6-112">Nakonfiguruje koncové body Kestrel v `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="5d5c6-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="5d5c6-113">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> naslouchat [soketu domény se systémem UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) se zadanou cestou.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="5d5c6-114">Kestrel má vestavěnou podporu pro koncové body UDS.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="5d5c6-115">Verze UDS je podporovaná v systémech Linux, macOS a [moderních verzích Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="5d5c6-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="5d5c6-116">Konfigurace klienta</span><span class="sxs-lookup"><span data-stu-id="5d5c6-116">Client configuration</span></span>

<span data-ttu-id="5d5c6-117">`GrpcChannel` podporuje vytváření volání gRPC pomocí vlastních přenosů.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="5d5c6-118">Při vytvoření kanálu je možné ho nakonfigurovat s `SocketsHttpHandler` vlastním `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="5d5c6-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="5d5c6-119">Továrna umožňuje klientovi vytvářet připojení přes vlastní přenosy a pak odesílat požadavky HTTP prostřednictvím tohoto přenosu.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5d5c6-120">`ConnectionFactory` je nové rozhraní API v rozhraní .NET 5 Release Candidate 1.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="5d5c6-121">Příklad vytváření připojení k doménovým soketům UNIX:</span><span class="sxs-lookup"><span data-stu-id="5d5c6-121">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

<span data-ttu-id="5d5c6-122">Vytvoření kanálu pomocí vlastního objektu pro vytváření připojení:</span><span class="sxs-lookup"><span data-stu-id="5d5c6-122">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="5d5c6-123">Kanály vytvořené pomocí předchozího kódu odesílají gRPC volání prostřednictvím doménových soketů systému UNIX.</span><span class="sxs-lookup"><span data-stu-id="5d5c6-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
