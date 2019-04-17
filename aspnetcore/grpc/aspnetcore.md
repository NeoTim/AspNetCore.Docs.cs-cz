---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: c99a499fad824c3ac026f6f390c826c0418fc069
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "58209022"
---
# <a name="grpc-services-with-aspnet-core"></a>Služby gRPC s ASP.NET Core

Tento dokument ukazuje, jak začít pracovat s gRPC služby pomocí ASP.NET Core.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Začínáme se službou gRPC v ASP.NET Core

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Zobrazit [začít pracovat se službami gRPC](xref:tutorials/grpc/grpc-start) podrobné pokyny o tom, jak vytvořit projekt gRPC.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

Spustit `dotnet new grpc -o GrpcGreeter` z příkazového řádku.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Přidání gRPC služby do aplikace ASP.NET Core

gRPC vyžaduje následující balíčky:

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf zpráva rozhraní API.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>Konfigurace gRPC

gRPC je povolená s `AddGrpc` metody:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

Každá služba gRPC se přidá do kanálu směrování prostřednictvím `MapGrpcService` metody:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

Funkce a ASP.NET Core middlewares sdílet směrování kanálu, proto aplikace může být nakonfigurován tak, aby obslužné rutiny další požadavek. Obslužné rutiny dalších požadavků, jako jsou řadiče MVC pracovat souběžně s nakonfigurovanou gRPC služby.

## <a name="integration-with-aspnet-core-apis"></a>Integrace s rozhraními API pro ASP.NET Core

gRPC služby, jako mají plný přístup k funkcím ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) a [protokolování](xref:fundamentals/logging/index). Implementace služby například můžete vyřešit služby protokoly z kontejnerů DI prostřednictvím konstruktoru:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Ve výchozím nastavení lze vyřešit implementace služby gRPC dalším službám DI pomocí jakékoli životnost (Singleton, obor nebo přechodným).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Vyřešit HttpContext gRPC metod

GRPC rozhraní API poskytuje přístup k některým datům zprávy HTTP/2, jako je metoda, hostitele, záhlaví a přípojných. Přístup je prostřednictvím `ServerCallContext` argument předaný metodě každou gRPC:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` neposkytuje úplný přístup k `HttpContext` v všechna rozhraní API technologie ASP.NET. `GetHttpContext` – Metoda rozšíření poskytuje úplný přístup ke `HttpContext` představující základní zprávy HTTP/2 v rozhraní API technologie ASP.NET:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

### <a name="request-body-data-rate-limit"></a>Omezení četnosti data těla požadavku

Ve výchozím nastavení, Kestrel server ukládá [minimální požadavek tělo přenosová rychlost](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>). Pro klienty streamování a streaming volání duplexní režim nemusí být splněny tato sazba a připojení může být vypršení časového limitu. Minimální požadavku limitu přenosové rychlosti dat musí být zakázáno, pokud obsahuje služba gRPC klienta streamování a streaming volání duplexní režim:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
