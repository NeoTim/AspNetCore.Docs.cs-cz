---
title: Poradce při potížích s gRPC v jádru rozhraní .NET
author: jamesnk
description: Poradce při potížích při použití gRPC na .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664127"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Poradce při potížích s gRPC v jádru rozhraní .NET

Podle [James Newton-King](https://twitter.com/jamesnk)

Tento dokument popisuje běžně se vyskytující problémy při vývoji gRPC aplikací na rozhraní .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Neshoda mezi konfigurací SSL/TLS klienta a služby

Šablona gRPC a ukázky používají [zabezpečení transportní vrstvy (TLS)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení. gRPC klienti musí k úspěšnému volání zabezpečených služeb gRPC používat zabezpečené připojení.

Můžete ověřit, ASP.NET služba Core gRPC používá TLS v protokolech napsaných při spuštění aplikace. Služba bude poslouchat na koncovém bodu HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Klient .NET Core `https` musí použít v adrese serveru volání se zabezpečeným připojením:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Všechny implementace klientů gRPC podporují TLS. klienti gRPC z jiných jazyků obvykle `SslCredentials`vyžadují kanál nakonfigurovaný pomocí aplikace . `SslCredentials`určuje certifikát, který bude klient používat, a musí být použit namísto nezabezpečených pověření. Příklady konfigurace různých implementací klienta gRPC pro použití Protokolu TLS naleznete [v tématu gRPC Authentication](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Volání služby gRPC s nedůvěryhodným/neplatným certifikátem

Klient gRPC rozhraní vyžaduje, aby služba měla důvěryhodný certifikát. Při volání služby gRPC bez důvěryhodného certifikátu je vrácena následující chybová zpráva:

> Neošetřená výjimka. System.Net.Http.HttpRequestException: Připojení SSL nelze navázat, viz vnitřní výjimka.
> ---> System.Security.Authentication.AuthenticationException: Vzdálený certifikát je podle ověřovacího postupu neplatný.

Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET vývojový certifikát Core HTTPS není důvěryhodný. Pokyny k vyřešení tohoto problému naleznete [v tématu Trust ASP.NET core https development certificate ve Windows a macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Pokud voláte službu gRPC v jiném počítači a nemůžete certifikátu důvěřovat, pak lze klienta gRPC nakonfigurovat tak, aby ignoroval neplatný certifikát. Následující kód používá [httpClientHandler.ServerCertificateValidationValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Nedůvěryhodné certifikáty by se měly používat pouze během vývoje aplikací. Produkční aplikace by měly vždy používat platné certifikáty.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Volání nezabezpečených služeb gRPC s klientem .NET Core

Další konfigurace je vyžadována pro volání nezabezpečené služby gRPC s klientem .NET Core. Klient gRPC musí `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit `true` přepínač `http` a použít v adrese serveru:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Nelze spustit aplikaci core gRPC ASP.NET v systému macOS

Kestrel nepodporuje HTTP/2 s TLS v systému macOS a starších verzích systému Windows, jako je Windows 7. Šablona ASP.NET Core gRPC a ukázky ve výchozím nastavení používají TLS. Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:

> Nelze vytvořit https://localhost:5001 vazbu na rozhraní zpětné smyčky IPv4: "HTTP/2 přes TLS není v systému macOS podporován z důvodu chybějící podpory ALPN.'.

Chcete-li tento problém vyřešit, nakonfigurujte Kestrel a klientgRPC používat HTTP/2 *bez* TLS. Měli byste to provést pouze během vývoje. Nepoužívání TLS bude mít za následek gRPC zprávy odesílané bez šifrování.

Poštolka musí nakonfigurovat koncový bod HTTP/2 bez TLS v *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Pokud je koncový bod HTTP/2 nakonfigurován bez protokolu TLS, musí být `HttpProtocols.Http2`protokol [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na . `HttpProtocols.Http1AndHttp2`nelze použít, protože tls je nutné vyjednat HTTP/2. Bez TLS všechna připojení ke koncovému bodu výchozí HTTP/1.1 a gRPC volání nezdaří.

Klient gRPC musí být také nakonfigurován tak, aby nepoužíval TLS. Další informace naleznete [v tématu Volání nezabezpečených služeb gRPC s klientem .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 bez TLS by se měl používat pouze během vývoje aplikace. Produkční aplikace by měly vždy používat zabezpečení přenosu. Další informace naleznete [v tématu Důležité informace o zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>GRPC C# assets are not code generated from .proto files gRPC C# assets are not code generated from .proto files gRPC C# assets are not code generated from .proto files gR

gRPC generování kódu konkrétních klientů a základní třídy služeb vyžaduje protobuf soubory a nástroje, které mají být odkazovány z projektu. Musíte uvést:

* *.proto* soubory, které chcete `<Protobuf>` použít ve skupině položek. [Importované soubory *.proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být projektem odkazovány.
* Odkaz na balíček gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Další informace o generování prostředků gRPC <xref:grpc/basics>C# naleznete v tématu .

Ve výchozím `<Protobuf>` nastavení odkaz generuje konkrétníklienta a základní třídu služby. Atribut referenčního `GrpcServices` prvku lze použít k omezení generování majetku jazyka C#. Platné `GrpcServices` možnosti jsou:

* `Both`(výchozí, pokud není přítomen)
* `Server`
* `Client`
* `None`

ASP.NET Základní webová aplikace hostující služby gRPC potřebuje pouze vygenerovanou třídu služby:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Klientská aplikace gRPC, která provádí volání gRPC, potřebuje pouze konkrétního klienta generovaného:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>WPF projekty nelze generovat gRPC C# prostředky ze souborů .proto

WPF projekty mají [známý problém,](https://github.com/dotnet/wpf/issues/810) který brání generování kódu gRPC pracovat správně. Všechny typy gRPC generované v projektu WPF odkazem `Grpc.Tools` a *.proto* soubory vytvoří chyby kompilace při použití:

> chyba CS0246: Typ nebo název oboru názvů MyGrpcServices nebyl nalezen (chybí vám direktiva using nebo odkaz na sestavení?)

Tento problém můžete vyřešit takto:

1. Vytvořte nový projekt knihovny tříd .NET Core.
2. V novém projektu přidejte odkazy, které umožní [generování kódu Jazyka C# ze * \** souborů .proto](xref:grpc/basics#generated-c-assets):
    * Přidejte odkaz na balíček do balíčku [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
    * Přidejte * \** soubory .proto do skupiny `<Protobuf>` položek.
3. V aplikaci WPF přidejte odkaz na nový projekt.

Aplikace WPF můžete použít gRPC generované typy z projektu nové knihovny tříd.

[!INCLUDE[](~/includes/gRPCazure.md)]
