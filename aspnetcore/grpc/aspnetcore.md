---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Naučte se základní pojmy při psaní služeb gRPC s ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667627"
---
# <a name="grpc-services-with-aspnet-core"></a>Služby gRPC s ASP.NET Core

Tento dokument ukazuje, jak začít se službami gRPC pomocí ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Začínáme se službou gRPC v ASP.NET Core

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak stáhnout](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Podrobné pokyny k vytvoření projektu gRPC najdete v tématu [Začínáme se službami gRPC.](xref:tutorials/grpc/grpc-start)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio pro Mac](#tab/visual-studio-code+visual-studio-mac)

Spusťte `dotnet new grpc -o GrpcGreeter` z příkazového řádku.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Přidání služeb gRPC do aplikace ASP.NET Core

gRPC vyžaduje balíček [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)

### <a name="configure-grpc"></a>Konfigurace gRPC

V *Startup.cs*:

* gRPC je s `AddGrpc` metodou povolena.
* Každá služba gRPC je přidána `MapGrpcService` do kanálu směrování prostřednictvím metody.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET základní middlewares a funkce sdílet směrování kanálu, proto aplikace může být nakonfigurován tak, aby sloužit další obslužné rutiny požadavku. Další obslužné rutiny požadavků, jako jsou řadiče MVC, pracují paralelně s nakonfigurovanými službami gRPC.

### <a name="configure-kestrel"></a>Konfigurovat poštolky

Kestrel gRPC koncové body:

* Vyžadovat HTTP/2.
* By měla být zabezpečena [pomocí zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246)

#### <a name="http2"></a>HTTP/2

gRPC vyžaduje HTTP/2. gRPC pro ASP.NET Core ověřuje [httprequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) je `HTTP/2`.

Kestrel [podporuje HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na většině moderních operačních systémů. Koncové body Kestrel jsou ve výchozím nastavení nakonfigurovány tak, aby podporovaly připojení HTTP/1.1 a HTTP/2.

#### <a name="tls"></a>TLS

Kestrelové koncové body používané pro gRPC by měly být zajištěny TLS. Ve vývoji koncový bod zabezpečený tls je automaticky vytvořen při `https://localhost:5001` ASP.NET je k dispozici certifikát vývoje jádra. Není nutná žádná konfigurace. Předpona `https` ověří koncový bod Kestrel používá TLS.

V produkčním prostředí musí být tls explicitně nakonfigurován. V následujícím příkladu *appsettings.json* je k dispozici koncový bod HTTP/2 zabezpečený pomocí tls:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Případně kestrel koncové body lze nakonfigurovat v *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Vyjednávání protokolu

TLS se používá pro více než zajištění komunikace. Handshake protokolu [tls aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) se používá k vyjednání protokolu připojení mezi klientem a serverem, pokud koncový bod podporuje více protokolů. Toto vyjednávání určuje, zda připojení používá HTTP/1.1 nebo HTTP/2.

Pokud je koncový bod HTTP/2 nakonfigurován bez protokolu TLS, musí být `HttpProtocols.Http2`protokol [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na . Koncový bod s více protokoly `HttpProtocols.Http1AndHttp2`(například) nelze použít bez TLS, protože neexistuje žádné vyjednávání. Všechna připojení k nezabezpečenému koncovému bodu výchozí HTTP/1.1 a gRPC volání nezdaří.

Další informace o povolení HTTP/2 a TLS s Kestrel, viz [Kestrel konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS nepodporuje ASP.NET Core gRPC s TLS. K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace. Další informace najdete [v tématu Nelze spustit ASP.NET aplikaci Core gRPC v systému macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integrace s ASP.NET základními API

služby gRPC mají plný přístup k ASP.NET core funkce, jako je [vkládání závislostí](xref:fundamentals/dependency-injection) (DI) a [protokolování](xref:fundamentals/logging/index). Implementace služby může například vyřešit službu protokolování z kontejneru DI prostřednictvím konstruktoru:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Ve výchozím nastavení může implementace služby gRPC vyřešit další služby DI s libovolnou životností (Singleton, Scoped nebo Transient).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Vyřešit httpcontext v metodách gRPC

Rozhraní gRPC API poskytuje přístup k některým datům zpráv HTTP/2, jako je metoda, hostitel, záhlaví a přívěsy. Přístup je `ServerCallContext` prostřednictvím argumentu předaný každé metodě gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`neposkytuje úplný přístup `HttpContext` ke všem ASP.NET api. Metoda `GetHttpContext` rozšíření poskytuje úplný `HttpContext` přístup k představující základní zprávu HTTP/2 v ASP.NET API:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
