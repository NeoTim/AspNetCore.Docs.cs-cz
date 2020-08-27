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
# <a name="inter-process-communication-with-grpc"></a>Komunikace mezi procesy pomocí gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

gRPC volání mezi klientem a službou se obvykle odesílají přes sokety TCP. Protokol TCP je ideální pro komunikaci přes síť, ale [komunikace mezi procesy (IPC)](https://wikipedia.org/wiki/Inter-process_communication) je efektivnější, pokud je klient a služba ve stejném počítači. Tento dokument vysvětluje, jak používat gRPC s vlastními přenosy ve scénářích IPC.

## <a name="server-configuration"></a>Konfigurace serveru

Vlastní přenos podporuje Kestrel. Kestrel se konfiguruje v *program.cs*:

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
* Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> naslouchat [soketu domény se systémem UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) se zadanou cestou.

Kestrel má vestavěnou podporu pro koncové body UDS. Verze UDS je podporovaná v systémech Linux, macOS a [moderních verzích Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Konfigurace klienta

`GrpcChannel` podporuje vytváření volání gRPC pomocí vlastních přenosů. Při vytvoření kanálu je možné ho nakonfigurovat s `SocketsHttpHandler` vlastním `ConnectionFactory` . Továrna umožňuje klientovi vytvářet připojení přes vlastní přenosy a pak odesílat požadavky HTTP prostřednictvím tohoto přenosu.

> [!IMPORTANT]
> `ConnectionFactory` je nové rozhraní API v rozhraní .NET 5 Release Candidate 1.

Příklad vytváření připojení k doménovým soketům UNIX:

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

Vytvoření kanálu pomocí vlastního objektu pro vytváření připojení:

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

Kanály vytvořené pomocí předchozího kódu odesílají gRPC volání prostřednictvím doménových soketů systému UNIX.
