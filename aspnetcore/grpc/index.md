---
<span data-ttu-id="49d2a-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="49d2a-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="49d2a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49d2a-102">'Blazor'</span></span>
- <span data-ttu-id="49d2a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49d2a-103">'Identity'</span></span>
- <span data-ttu-id="49d2a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49d2a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="49d2a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49d2a-105">'Razor'</span></span>
- <span data-ttu-id="49d2a-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="49d2a-106">'SignalR' uid:</span></span> 

---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="49d2a-107">Úvod do gRPC v .NET Core</span><span class="sxs-lookup"><span data-stu-id="49d2a-107">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="49d2a-108">Od [Jan Luo](https://github.com/juntaoluo) a [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="49d2a-108">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="49d2a-109">[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="49d2a-109">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="49d2a-110">Hlavní výhody gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="49d2a-110">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="49d2a-111">Moderní, vysoce výkonné a odlehčené rozhraní RPC.</span><span class="sxs-lookup"><span data-stu-id="49d2a-111">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="49d2a-112">Vývoj rozhraní API kontraktu, ve výchozím nastavení používá vyrovnávací paměti protokolů, což umožňuje implementaci nezávislá jazyka.</span><span class="sxs-lookup"><span data-stu-id="49d2a-112">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="49d2a-113">Nástroj, který je k dispozici pro mnoho jazyků pro generování serverů a klientů se silným typem.</span><span class="sxs-lookup"><span data-stu-id="49d2a-113">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="49d2a-114">Podporuje volání klientů, serverů a obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="49d2a-114">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="49d2a-115">Omezené využití sítě pomocí binární serializace Protobuf</span><span class="sxs-lookup"><span data-stu-id="49d2a-115">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="49d2a-116">Díky těmto výhodám je gRPC ideální pro:</span><span class="sxs-lookup"><span data-stu-id="49d2a-116">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="49d2a-117">Odlehčené mikroslužby, ve kterých je efektivita nejdůležitější.</span><span class="sxs-lookup"><span data-stu-id="49d2a-117">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="49d2a-118">Polyglot systémy, ve kterých se pro vývoj vyžaduje více jazyků.</span><span class="sxs-lookup"><span data-stu-id="49d2a-118">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="49d2a-119">Služby v reálném čase Point-to-Point, které potřebují zpracovávat žádosti o streamování nebo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="49d2a-119">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="49d2a-120">Podpora nástrojů C# pro soubory.</span><span class="sxs-lookup"><span data-stu-id="49d2a-120">C# Tooling support for .proto files</span></span>

<span data-ttu-id="49d2a-121">gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem.</span><span class="sxs-lookup"><span data-stu-id="49d2a-121">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="49d2a-122">Služby a zprávy jsou definovány v souboru \* \* . proto\* soubory:</span><span class="sxs-lookup"><span data-stu-id="49d2a-122">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="49d2a-123">Typy .NET pro služby, klienti a zprávy jsou automaticky vygenerováni \* \* zahrnutím souborů do\* projektu:</span><span class="sxs-lookup"><span data-stu-id="49d2a-123">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="49d2a-124">Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="49d2a-124">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="49d2a-125">Do skupiny položek přidejte soubory \* \* ..\* . `<Protobuf>`</span><span class="sxs-lookup"><span data-stu-id="49d2a-125">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="49d2a-126">Další informace o podpoře nástrojů gRPC naleznete v tématu <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="49d2a-126">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="49d2a-127">gRPC Services na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49d2a-127">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="49d2a-128">služby gRPC lze hostovat na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49d2a-128">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="49d2a-129">Služby mají úplnou integraci s oblíbenými ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.</span><span class="sxs-lookup"><span data-stu-id="49d2a-129">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="49d2a-130">Šablona projektu služby gRPC poskytuje počáteční službu:</span><span class="sxs-lookup"><span data-stu-id="49d2a-130">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="49d2a-131">`GreeterService`dědí z `GreeterBase` typu, který je vygenerován ze `Greeter` služby v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="49d2a-131">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="49d2a-132">Služba je zpřístupněna klientům v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="49d2a-132">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="49d2a-133">Další informace o službách gRPC Services v ASP.NET Core najdete v tématu <xref:grpc/aspnetcore> .</span><span class="sxs-lookup"><span data-stu-id="49d2a-133">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="49d2a-134">Volání služeb gRPC Services pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="49d2a-134">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="49d2a-135">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze souborů \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="49d2a-135">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="49d2a-136">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="49d2a-136">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="49d2a-137">Klient gRPC se vytvoří pomocí kanálu, který představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="49d2a-137">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="49d2a-138">Kanál se dá vytvořit pomocí `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="49d2a-138">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="49d2a-139">Další informace o vytváření klientů a volání různých metod služby najdete v tématu <xref:grpc/client> .</span><span class="sxs-lookup"><span data-stu-id="49d2a-139">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49d2a-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49d2a-140">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
