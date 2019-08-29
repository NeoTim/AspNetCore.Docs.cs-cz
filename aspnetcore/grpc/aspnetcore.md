---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Seznamte se se základními pojmy při psaní služeb gRPC pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130708"
---
# <a name="grpc-services-with-aspnet-core"></a>Služby gRPC s ASP.NET Core

Tento dokument ukazuje, jak začít s gRPC službami pomocí ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Začínáme se službou gRPC v ASP.NET Core

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu gRPC najdete v tématu Začínáme [se službou gRPC Services](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte `dotnet new grpc -o GrpcGreeter` příkaz z příkazového řádku.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Přidání služeb gRPC Services do aplikace ASP.NET Core

gRPC vyžaduje balíček [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Konfigurace gRPC

V *Startup.cs*:

* gRPC je povoleno s `AddGrpc` metodou.
* Každá služba gRPC je prostřednictvím `MapGrpcService` metody přidána do kanálu směrování.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

ASP.NET Core middlewarů a funkcí sdílí kanál směrování, proto je možné aplikaci nakonfigurovat tak, aby poskytovala další obslužné rutiny žádostí. Další obslužné rutiny žádostí, jako jsou například řadiče MVC, pracují paralelně s nakonfigurovanými gRPC službami.

### <a name="configure-kestrel"></a>Konfigurace Kestrel

Koncové body Kestrel gRPC:

* Vyžadovat HTTP/2.
* By měl být zabezpečený pomocí protokolu HTTPS.

#### <a name="http2"></a>HTTP/2

Kestrel [podporuje HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na většině moderních operačních systémů. Ve výchozím nastavení jsou Kestrel koncové body konfigurovány pro podporu připojení HTTP/1.1 a HTTP/2.

> [!NOTE]
> macOS nepodporuje ASP.NET Core gRPC se [zabezpečením TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246). K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace. Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

#### <a name="https"></a>HTTPS

Koncové body Kestrel použité pro gRPC by měly být zabezpečené pomocí protokolu HTTPS. Ve vývoji se koncový bod HTTPS automaticky vytvoří `https://localhost:5001` , když je k dispozici ASP.NET Core vývojový certifikát. Není nutná žádná konfigurace.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. V následujícím příkladu *appSettings. JSON* je k dispozici koncový bod HTTP/2 zabezpečený pomocí protokolu https:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternativně lze Kestrel endspoints nakonfigurovat v *program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

Další informace o povolení HTTP/2 a HTTPS s Kestrel najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="integration-with-aspnet-core-apis"></a>Integrace s rozhraními API ASP.NET Core

Služba gRPC Services má plný přístup k funkcím ASP.NET Core, jako je například [vkládání závislostí](xref:fundamentals/dependency-injection) (di) a [protokolování](xref:fundamentals/logging/index). Například implementace služby může přeložit službu protokolovacího nástroje z kontejneru DI prostřednictvím konstruktoru:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Ve výchozím nastavení může implementace služby gRPC vyřešit jiné služby DI Services s jakoukoli životností (singleton, vymezený nebo přechodný).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Vyřešit HttpContext v metodách gRPC

Rozhraní gRPC API poskytuje přístup k některým datům zprávy HTTP/2, jako je například metoda, hostitel, hlavička a přípojná část. Přístup je prostřednictvím `ServerCallContext` argumentu předaného pro každou metodu gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`neposkytuje úplný přístup ke `HttpContext` všem rozhraním API ASP.NET. Metoda rozšíření poskytuje úplný přístup `HttpContext` k reprezentující základní zprávu HTTP/2 v rozhraních API ASP.NET: `GetHttpContext`

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
