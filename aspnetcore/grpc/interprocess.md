---
title: Komunikace mezi procesy pomocí služby gRPC
author: jamesnk
description: Naučte se používat gRPC pro komunikaci mezi procesy.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
ms.openlocfilehash: 34876f31cbc51ba66a91ae32ea6a5213dc34a369
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770152"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="55e49-103">Komunikace mezi procesy pomocí služby gRPC</span><span class="sxs-lookup"><span data-stu-id="55e49-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="55e49-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="55e49-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="55e49-105">gRPC volání mezi klientem a službou se obvykle odesílají přes sokety TCP.</span><span class="sxs-lookup"><span data-stu-id="55e49-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="55e49-106">Protokol TCP byl navržen pro komunikaci přes síť.</span><span class="sxs-lookup"><span data-stu-id="55e49-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="55e49-107">[Komunikace mezi procesy (IPC)](https://wikipedia.org/wiki/Inter-process_communication) je efektivnější než TCP, pokud je klient a služba ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="55e49-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="55e49-108">Tento dokument vysvětluje, jak používat gRPC s vlastními přenosy ve scénářích IPC.</span><span class="sxs-lookup"><span data-stu-id="55e49-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="55e49-109">Konfigurace serveru</span><span class="sxs-lookup"><span data-stu-id="55e49-109">Server configuration</span></span>

<span data-ttu-id="55e49-110">Vlastní přenos podporuje [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="55e49-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="55e49-111">Kestrel se konfiguruje v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="55e49-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="55e49-112">Předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="55e49-112">The preceding example:</span></span>

* <span data-ttu-id="55e49-113">Nakonfiguruje koncové body Kestrel v `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="55e49-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="55e49-114">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> naslouchat [soketu domény se systémem UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) se zadanou cestou.</span><span class="sxs-lookup"><span data-stu-id="55e49-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="55e49-115">Kestrel má vestavěnou podporu pro koncové body UDS.</span><span class="sxs-lookup"><span data-stu-id="55e49-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="55e49-116">Verze UDS je podporovaná v systémech Linux, macOS a [moderních verzích Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="55e49-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="55e49-117">Konfigurace klienta</span><span class="sxs-lookup"><span data-stu-id="55e49-117">Client configuration</span></span>

<span data-ttu-id="55e49-118">`GrpcChannel` podporuje vytváření volání gRPC pomocí vlastních přenosů.</span><span class="sxs-lookup"><span data-stu-id="55e49-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="55e49-119">Při vytvoření kanálu je možné ho nakonfigurovat s `SocketsHttpHandler` vlastním `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="55e49-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="55e49-120">Zpětné volání umožňuje klientovi vytvořit připojení přes vlastní přenosy a pak odesílat požadavky HTTP prostřednictvím tohoto přenosu.</span><span class="sxs-lookup"><span data-stu-id="55e49-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="55e49-121">`SocketsHttpHandler.ConnectCallback` je nové rozhraní API v rozhraní .NET 5 Release Candidate 2.</span><span class="sxs-lookup"><span data-stu-id="55e49-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="55e49-122">Příklad vytváření připojení k doménovým soketům UNIX:</span><span class="sxs-lookup"><span data-stu-id="55e49-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="55e49-123">Vytvoření kanálu pomocí vlastního objektu pro vytváření připojení:</span><span class="sxs-lookup"><span data-stu-id="55e49-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="55e49-124">Kanály vytvořené pomocí předchozího kódu odesílají gRPC volání prostřednictvím doménových soketů systému UNIX.</span><span class="sxs-lookup"><span data-stu-id="55e49-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="55e49-125">Podporu jiných technologií IPC lze implementovat pomocí rozšíření v Kestrel a `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="55e49-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
