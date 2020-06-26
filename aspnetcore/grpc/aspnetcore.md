---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Seznamte se se základními pojmy při psaní služeb gRPC pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/aspnetcore
ms.openlocfilehash: 0d05a6dcaf6677e71181d522a9f501051ec34f9d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407548"
---
# <a name="grpc-services-with-aspnet-core"></a>Služby gRPC s ASP.NET Core

Tento dokument ukazuje, jak začít s gRPC službami pomocí ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Začínáme se službou gRPC v ASP.NET Core

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu gRPC najdete v tématu Začínáme [se službou gRPC Services](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte `dotnet new grpc -o GrpcGreeter` příkaz z příkazového řádku.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Přidání služeb gRPC Services do aplikace ASP.NET Core

gRPC vyžaduje balíček [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .

### <a name="configure-grpc"></a>Konfigurace gRPC

V *Startup.cs*:

* gRPC je povoleno s `AddGrpc` metodou.
* Každá služba gRPC je prostřednictvím metody přidána do kanálu směrování `MapGrpcService` .

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core middlewarů a funkcí sdílí kanál směrování, proto je možné aplikaci nakonfigurovat tak, aby poskytovala další obslužné rutiny žádostí. Další obslužné rutiny žádostí, jako jsou například řadiče MVC, pracují paralelně s nakonfigurovanými gRPC službami.

### <a name="configure-kestrel"></a>Konfigurace Kestrel

Koncové body Kestrel gRPC:

* Vyžadovat HTTP/2.
* By měl být zabezpečený pomocí [protokolu TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).

#### <a name="http2"></a>HTTP/2

gRPC vyžaduje HTTP/2. gRPC pro ASP.NET Core ověří [HttpRequest. protokol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) je `HTTP/2` .

Kestrel [podporuje HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na většině moderních operačních systémů. Ve výchozím nastavení jsou Kestrel koncové body konfigurovány pro podporu připojení HTTP/1.1 a HTTP/2.

#### <a name="tls"></a>TLS

Koncové body Kestrel použité pro gRPC by měly být zabezpečené pomocí protokolu TLS. Ve vývojovém prostředí je koncový bod zabezpečený pomocí protokolu TLS automaticky vytvořen v době `https://localhost:5001` , kdy je k dispozici ASP.NET Core vývojový certifikát. Není nutná žádná konfigurace. `https`Předpona ověří koncový bod Kestrel pomocí protokolu TLS.

V produkčním prostředí musí být protokol TLS explicitně nakonfigurovaný. V následujících *appsettings.jsv* tomto příkladu je k dispozici koncový bod HTTP/2 zabezpečený pomocí protokolu TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativně lze v *program.cs*nakonfigurovat koncové body Kestrel:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Vyjednávání protokolu

Protokol TLS se používá pro více než zabezpečení komunikace. Metoda handshake [protokolu TLS (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) se používá k vyjednání protokolu připojení mezi klientem a serverem, když koncový bod podporuje více protokolů. Toto vyjednávání určuje, zda připojení používá protokol HTTP/1.1 nebo HTTP/2.

Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na `HttpProtocols.Http2` . Koncový bod s více protokoly (například `HttpProtocols.Http1AndHttp2` ) nelze použít bez TLS, protože neexistuje žádné vyjednávání. Všechna připojení k nezabezpečenému koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC selžou.

Další informace o povolení HTTP/2 a TLS s Kestrel najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS nepodporuje ASP.NET Core gRPC s protokolem TLS. K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace. Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

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

`ServerCallContext`neposkytuje úplný přístup ke `HttpContext` všem rozhraním api ASP.NET. `GetHttpContext`Metoda rozšíření poskytuje úplný přístup k `HttpContext` reprezentující základní zprávu HTTP/2 v rozhraních API ASP.NET:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
