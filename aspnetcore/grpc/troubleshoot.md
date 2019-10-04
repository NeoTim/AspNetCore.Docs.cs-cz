---
title: Řešení potíží s gRPC pro .NET Core
author: jamesnk
description: Řešení chyb při použití gRPC v .NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/21/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c31f499b008cdec9d759e804b18965156ca99f30
ms.sourcegitcommit: d8b12cc1716ee329d7bd2300e201b61e15d506ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71942893"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Řešení potíží s gRPC pro .NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

Tento dokument popisuje běžně zjištěné problémy při vývoji aplikací gRPC na platformě .NET.

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
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Všechny implementace klientů gRPC podporují protokol TLS. klienti gRPC z jiných jazyků obvykle vyžadují kanál konfigurovaný pomocí `SslCredentials`. `SslCredentials`Určuje certifikát, který bude klient používat, a musí se používat místo nezabezpečených přihlašovacích údajů. Příklady konfigurace různých implementací klientů gRPC k použití protokolu TLS najdete v tématu [ověřování gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Volání služby gRPC s nedůvěryhodným/neplatným certifikátem

Klient .NET gRPC vyžaduje, aby služba měla důvěryhodný certifikát. Při volání služby gRPC bez důvěryhodného certifikátu se vrátí následující chybová zpráva:

> Neošetřená výjimka. System.Net.Http.HttpRequestException: Nebylo možné navázat připojení SSL, viz vnitřní výjimka.
> ---> System. Security. Authentication. AuthenticationException –: Vzdálený certifikát je podle ověřovací procedury neplatný.

Tato chyba se může zobrazit, pokud testujete aplikaci místně a ASP.NET Core certifikát pro vývoj HTTPS není důvěryhodný. Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Pokud voláte službu gRPC na jiném počítači a nemůžete důvěřovat certifikátu, může být klient gRPC nakonfigurovaný tak, aby ignoroval neplatný certifikát. Následující kód používá [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) k povolení volání bez důvěryhodného certifikátu:

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
> Nedůvěryhodné certifikáty by se měly používat jenom při vývoji aplikací. Produkční aplikace by měly vždy používat platné certifikáty.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Volání nezabezpečených služeb gRPC pomocí klienta .NET Core

Pro volání nezabezpečených služeb gRPC s klientem .NET Core je vyžadována další konfigurace. Klient gRPC musí na adrese serveru `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit přepínač `true` a použít `http` ho:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
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
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven `HttpProtocols.Http2`na. `HttpProtocols.Http1AndHttp2`nelze použít, protože pro vyjednání HTTP/2 je vyžadován protokol TLS. Bez TLS budou všechna připojení ke koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC neúspěšná.

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

## <a name="wpf-projects-unable-to-generated-grpc-c-assets-from-proto-files"></a>Projekty WPF nemohly vygenerovat gRPC C# prostředky z *@no__t -2. soubory.*

Projekty WPF mají [známý problém](https://github.com/dotnet/wpf/issues/810) , který brání správnému fungování generování kódu gRPC. Všechny typy gRPC generované v projektu WPF odkazem na `Grpc.Tools` a *. proto* soubory vytvoří chyby kompilace při použití:

> Chyba CS0246: Typ nebo název oboru názvů ' MyGrpcServices ' nebyl nalezen (nechybí Direktiva using nebo odkaz na sestavení?)

Tento problém můžete vyřešit pomocí těchto potíží:

1. Vytvořte nový projekt knihovny tříd .NET Core.
2. V novém projektu přidejte odkazy pro povolení [ C# generování kódu z *@no__t -3.* a soubory:
    * Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
    * Do`<Protobuf>` skupiny položek přidejte *\*soubory..* .
3. V aplikaci WPF přidejte odkaz na nový projekt.

Aplikace WPF může použít gRPC generované typy z nového projektu knihovny tříd.

[!INCLUDE[](~/includes/gRPCazure.md)]
