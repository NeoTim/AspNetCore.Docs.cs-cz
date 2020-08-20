---
title: Úvod do gRPC v .NET Core
author: juntaoluo
description: Přečtěte si o službách gRPC Services pomocí serveru Kestrel a ASP.NET Core stacku.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 4a2f86d906c7b268b1fb2a63c50a1b8f0dd2b154
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633185"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="1e4c8-103">Úvod do gRPC v .NET Core</span><span class="sxs-lookup"><span data-stu-id="1e4c8-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="1e4c8-104">Od [Jan Luo](https://github.com/juntaoluo) a [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1e4c8-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1e4c8-105">[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="1e4c8-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="1e4c8-106">Mezi výhody gRPC patří:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="1e4c8-107">Moderní, výkonná a jednoduchá architektura RPC</span><span class="sxs-lookup"><span data-stu-id="1e4c8-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="1e4c8-108">Vývoj rozhraní API začínající kontraktem, který standardně využívá vyrovnávací paměti protokolů, čímž umožňuje implementace nezávislé na jazycích</span><span class="sxs-lookup"><span data-stu-id="1e4c8-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="1e4c8-109">Nástroje dostupné pro řadu jazyků, se kterými lze generovat servery a klienty silného typu</span><span class="sxs-lookup"><span data-stu-id="1e4c8-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="1e4c8-110">Podpora volání klienta, serveru a obousměrného streamování</span><span class="sxs-lookup"><span data-stu-id="1e4c8-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="1e4c8-111">Menší využití sítě díky binární serializaci Protobuf</span><span class="sxs-lookup"><span data-stu-id="1e4c8-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="1e4c8-112">Díky těmto výhodám je gRPC ideální pro:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="1e4c8-113">Odlehčené mikroslužby, ve kterých je efektivita nejdůležitější.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="1e4c8-114">Polyglot systémy, ve kterých se pro vývoj vyžaduje více jazyků.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="1e4c8-115">Služby v reálném čase Point-to-Point, které potřebují zpracovávat žádosti o streamování nebo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="1e4c8-116">Podpora nástrojů C# pro soubory.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="1e4c8-117">gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="1e4c8-118">Služby a zprávy jsou definovány v souboru \* \* . proto\* soubory:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-118">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="1e4c8-119">Typy .NET pro služby, klienti a zprávy jsou automaticky vygenerováni \* \* zahrnutím souborů do\* projektu:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="1e4c8-120">Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="1e4c8-121">Do skupiny položek přidejte soubory \* \* ..\* . `<Protobuf>`</span><span class="sxs-lookup"><span data-stu-id="1e4c8-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="1e4c8-122">Další informace o podpoře nástrojů gRPC naleznete v tématu <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="1e4c8-123">gRPC Services na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e4c8-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="1e4c8-124">služby gRPC lze hostovat na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="1e4c8-125">Služby mají úplnou integraci s oblíbenými ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="1e4c8-126">Šablona projektu služby gRPC poskytuje počáteční službu:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-126">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="1e4c8-127">`GreeterService`dědí z `GreeterBase` typu, který je vygenerován ze `Greeter` služby v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="1e4c8-128">Služba je zpřístupněna klientům v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e4c8-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="1e4c8-129">Další informace o službách gRPC Services v ASP.NET Core najdete v tématu <xref:grpc/aspnetcore> .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="1e4c8-130">Volání služeb gRPC Services pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="1e4c8-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="1e4c8-131">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze souborů \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="1e4c8-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="1e4c8-132">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="1e4c8-133">Klient gRPC se vytvoří pomocí kanálu, který představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="1e4c8-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="1e4c8-134">Kanál se dá vytvořit pomocí `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="1e4c8-135">Další informace o vytváření klientů a volání různých metod služby najdete v tématu <xref:grpc/client> .</span><span class="sxs-lookup"><span data-stu-id="1e4c8-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e4c8-136">Další materiály</span><span class="sxs-lookup"><span data-stu-id="1e4c8-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
