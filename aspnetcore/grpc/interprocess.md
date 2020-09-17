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
ms.openlocfilehash: 7278ebd001aea4ba52c1134b3bac696c01950a27
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722991"
---
# <a name="inter-process-communication-with-grpc"></a>Komunikace mezi procesy pomocí služby gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

gRPC volání mezi klientem a službou se obvykle odesílají přes sokety TCP. Protokol TCP byl navržen pro komunikaci přes síť. [Komunikace mezi procesy (IPC)](https://wikipedia.org/wiki/Inter-process_communication) je efektivnější než TCP, pokud je klient a služba ve stejném počítači. Tento dokument vysvětluje, jak používat gRPC s vlastními přenosy ve scénářích IPC.

## <a name="server-configuration"></a>Konfigurace serveru

Vlastní přenos podporuje [Kestrel](xref:fundamentals/servers/kestrel). Kestrel se konfiguruje v *program.cs*:

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

Předchozí příklad:

* Nakonfiguruje koncové body Kestrel v `ConfigureKestrel` .
* Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> naslouchat [soketu domény se systémem UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) se zadanou cestou.

Kestrel má vestavěnou podporu pro koncové body UDS. Verze UDS je podporovaná v systémech Linux, macOS a [moderních verzích Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Konfigurace klienta

`GrpcChannel` podporuje vytváření volání gRPC pomocí vlastních přenosů. Při vytvoření kanálu je možné ho nakonfigurovat s `SocketsHttpHandler` vlastním `ConnectCallback` . Zpětné volání umožňuje klientovi vytvořit připojení přes vlastní přenosy a pak odesílat požadavky HTTP prostřednictvím tohoto přenosu.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` je nové rozhraní API v rozhraní .NET 5 Release Candidate 2.

Příklad vytváření připojení k doménovým soketům UNIX:

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

Vytvoření kanálu pomocí vlastního objektu pro vytváření připojení:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Kanály vytvořené pomocí předchozího kódu odesílají gRPC volání prostřednictvím doménových soketů systému UNIX. Podporu jiných technologií IPC lze implementovat pomocí rozšíření v Kestrel a `SocketsHttpHandler` .
