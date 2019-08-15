---
title: Řešení potíží s gRPC pro .NET Core
author: jamesnk
description: Řešení chyb při použití gRPC v .NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029078"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Řešení potíží s gRPC pro .NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Neshoda mezi konfigurací klienta a služby SSL/TLS

Šablona gRPC a ukázky používají protokol [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) k zabezpečení služeb gRPC ve výchozím nastavení. gRPC klienti musí používat zabezpečené připojení k úspěšnému volání zabezpečených služeb gRPC.

Můžete ověřit, že služba ASP.NET Core gRPC používá protokol TLS v protokolech zapsaných při spuštění aplikace. Služba bude naslouchat na koncovém bodu HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Klient .NET Core musí v adrese `https` serveru použít k volání zabezpečeného připojení:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

Všechny implementace klientů gRPC podporují protokol TLS. klienti gRPC z jiných jazyků obvykle vyžadují kanál konfigurovaný pomocí `SslCredentials`. `SslCredentials`Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů. Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Volání nezabezpečených služeb gRPC pomocí klienta .NET Core

Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace. Klient gRPC musí na adrese serveru `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit přepínač `true` a použít `http` ho:

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Nepovedlo se spustit aplikaci ASP.NET Core gRPC v macOS

Kestrel nepodporuje HTTP/2 s TLS v macOS a ve starších verzích Windows, jako je Windows 7. ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS. Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:

> Nedá se vytvořit vazba https://localhost:5001 na rozhraní zpětné smyčky IPv4: HTTP/2 přes TLS se v macOS nepodporuje kvůli chybějící podpoře ALPN.

Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 *bez* TLS. To byste měli udělat jenom během vývoje. Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.

Kestrel musí Konfigurovat koncový bod HTTP/2 bez TLS v *program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Klient gRPC musí být taky nakonfigurovaný tak, aby nepoužíval protokol TLS. Další informace najdete v tématu [Calling The gRPC Services with a .NET Core Client](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací. Provozní aplikace by měly vždycky používat zabezpečení přenosu. Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>prostředky C# gRPC nejsou generovány z  *\*kódu. proto* soubory

generování kódu gRPC konkrétní klienti a základní třídy služby vyžaduje soubory protobuf a nástroje, na které se odkazuje z projektu. Musíte zahrnout:

* *. proto* soubory, které chcete použít ve `<Protobuf>` skupině položek. [Importováno *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) musí být na soubory odkazováno v projektu.
* Odkaz na balíček nástrojů pro gRPC nástrojů pro [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).

Další informace o generování prostředků gRPC C# naleznete v tématu <xref:grpc/basics>.

Ve výchozím nastavení `<Protobuf>` odkaz vygeneruje konkrétního klienta a základní třídu služby. `GrpcServices` Atribut referenčního prvku lze použít k omezení C# generování prostředků. Platné `GrpcServices` možnosti jsou:

* `Both`(výchozí, není-li k dispozici)
* `Server`
* `Client`
* `None`

ASP.NET Core webová aplikace hostující služby gRPC potřebuje jenom vygenerovanou základní třídu služby:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Klientská aplikace gRPC, která provádí volání gRPC, potřebuje jenom konkrétního vygenerovaného klienta:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
